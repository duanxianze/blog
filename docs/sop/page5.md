---
description: 小程序切片上传视频，上传至华为云视频点播
title: 小程序切片上传视频
readingTime: false
tag:
 - 小程序,uniapp
recommend: 2
---

```js
import request from "@/request";
import CryptoJS from 'crypto-js';
async function getPre(title, video_name) {
  return request({
    url: "/api/beforeupload",
    data: {
      title,
      video_name
    },
    method: "post"
  })
}
function calculateBinaryMD5(data) {
  const md5 = CryptoJS.MD5(CryptoJS.lib.WordArray.create(data));
  return md5.toString(CryptoJS.enc.Base64);
}
// function splitVideo2(filePath) {
//   let tempArr = [];
//   return new Promise(resolve => {
//     uni.request({
//       url: filePath,
//       responseType: 'arraybuffer',
//       success: (res) => {
//         const arrayBuffer = res.data;
//         console.log(arrayBuffer)
//         console.log(calculateBinaryMD5(arrayBuffer))
//         // 进行分段操作
//         const segmentSize = 12 * 1000 * 1000; // 每段大小
//         for (let i = 0; i < arrayBuffer.byteLength; i += segmentSize) {
//           let segment = arrayBuffer.slice(i, i + segmentSize);
//           tempArr.push({
//             data: segment,
//             md5: calculateBinaryMD5(segment)
//           })
//         }
//         resolve(tempArr)
//       },
//       fail(err) {
//         console.log(err)
//       }
//     });
//   })
// }
function splitVideo(filePath) {
  let tempArr = [];
  const fileM = uni.getFileSystemManager();
  return new Promise(resolve => {
    fileM.readFile({
      filePath: filePath,
      success: (res) => {
        const arrayBuffer = res.data;
        // 进行分段操作
        const segmentSize = 12 * 1000 * 1000; // 每段大小
        for (let i = 0; i < arrayBuffer.byteLength; i += segmentSize) {
          let segment = arrayBuffer.slice(i, i + segmentSize);
          tempArr.push({
            data: segment,
            md5: calculateBinaryMD5(segment)
          })
        }
        resolve(tempArr)
      },
      fail(err) {
        console.log(err)
      }
    });
  })
}
function uploadVideo(files, upParem) {
  let upArr = [];
  for (let index = 0; index < files.length; index++) {
    const element = files[index];
    upArr.push(new Promise((resolve, rej) => {
      request({
        url: "/api/multipartupload",
        data: {
          bucket: upParem.target.bucket,
          object: upParem.target.object,
          upload_id: upParem.upload_id['0'],
          part_number: index + 1,
          md5: element.md5,
        },
        method: "post"
      }).then(async res => {
        let sign_str = res.sign_str;
        let data = undefined;
        for (let i = 0; i < 3; i++) {
          try {
            data = await uni.request({
              url: sign_str.replace(":443", ""),
              method: "put",
              header: {
                "Content-Type": "application/octet-stream",
                "Content-MD5": element.md5,
              },
              data: element.data,
            })
            if (data) {
              break;
            }
          } catch (error) {
            continue;
          }
        }
        if (data) {
          resolve(data)
        } else {
          rej(data)
        }
      })
    }))
  }
  return Promise.all(upArr)
}
function confirmUpload(upParem) {
  return request({
    url: "/api/afterupload",
    data: {
      bucket: upParem.target.bucket,
      object: upParem.target.object,
      upload_id: upParem.upload_id['0'],
      asset_id: upParem.asset_id,
    },
    method: "post"
  })
}
function getDeatil(asset_id) {
  return request({
    url: "/api/getdetail",
    data: {
      asset_id,
      categories: "base_info,transcode_info&thumbnail_info,review_info"
    },
    method: "post"
  })
}
export default async function (filePath) {
  let fileName = filePath.substring(filePath.lastIndexOf("/") + 1);
  let titleT = fileName.substring(0, fileName.lastIndexOf("."));
  let upParem = await getPre(titleT, fileName);
  let files = await splitVideo(filePath)
  // await splitVideo2(filePath)
  // console.log(files)
  let uploadRes = await uploadVideo(files, upParem);
  let conFirmRes = await confirmUpload(upParem)
  let info = await getDeatil(conFirmRes.asset_id);
  let url = `${info.base_info.video_url}?asset_id=${info.asset_id}`
  return url;
}
```

使用Uniapp,h5可使用splitVideo2