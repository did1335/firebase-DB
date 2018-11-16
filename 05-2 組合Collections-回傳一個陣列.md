# 05-2 組合Collections-回傳一個陣列

## (1) 組合students及scores二個Collections的內容

```
<測試>
   |__ index.js
   |
   |__ <lib>
         |__ db.js  
```

### (1-1) 資料庫的規則

```javascript
{
    /* Visit https://firebase.google.com/docs/database/security to learn more about security rules. */
    "rules": {
        ".read": true,
        ".write": true
    }  
}
```



### (1-2) db.js

```javascript
var firebase = require('firebase');
 
//------------------------
// firebase設定
//------------------------
var config =  {
    apiKey: "(自己在firebase的資料)",
    authDomain: "(自己在firebase的資料)",
    databaseURL: "(自己在firebase的資料)",
    projectId: "(自己在firebase的資料)",
    storageBucket: "(自己在firebase的資料)",
    messagingSenderId: "(自己在firebase的資料)"
};

//----------------------------
// 匯出firebase資料庫物件
//----------------------------
firebase.initializeApp(config);
module.exports = firebase.database();
```



### (1-3) index.js

```javascript
//----------------------------
// 引用db.js
//----------------------------
var myDB = require('./lib/db.js');

//----------------------------
// 讀入一筆資料的函式
//----------------------------
function readData(type){    
    return new Promise(function(resolve, reject){
        myDB.ref('students').orderByChild('type').equalTo(type).once('value').then(studentsSnap => {
            myDB.ref('/scores/' + Object.keys(studentsSnap.val())[0]).once('value').then(scoresSnap => {
                if (!scoresSnap.exists() || !studentsSnap.exists()) {                    
                    resolve([]);  //回傳空資料
                }else{
                    results = [];
					
                    studentsSnap.forEach(stuChild => {
                        //-------------------------
                        // 取出students快照內容
                        //-------------------------
                        stuObj = {};       
                        stuObj.stuNo = stuChild.key;
                        stuObj.name = stuChild.val().name;
                        stuObj.gender = stuChild.val().gender;
                        stuObj.school = stuChild.val().school;					
                        stuObj.type = stuChild.val().type;

                        //-------------------------
                        // 取出scores快照內容
                        //-------------------------					
                        scores=[];
								
                        scoresSnap.forEach(scoChild => {
                            var scoObj={};
                            key = scoChild.key;
                            var value = scoChild.val();
                            scoObj[key] = value;
											
                            scores.push(scoObj);
                        });	

                        //-------------------------
                        // 合併學生與成績資料
                        //-------------------------	
                        stuObj.scores = scores;
						
                        //-------------------------
                        // 將物件加入results中
                        //-------------------------							
                        results.push(stuObj);						
                    });                   
					
                    //-------------------------
                    // 回傳資料
                    //-------------------------						                                
                    resolve(results);
                    //-------------------------						
                }				
            });
        });       
    });
}


//----------------------------
// 主程式
//----------------------------
readData('原住民生').then(results => {
    results.forEach(doc => {
        console.log(doc.stuNo);
        console.log(doc.name);
        console.log(doc.gender);
        console.log(doc.school);
        console.log(doc.type);
        console.log(doc.scores);		
        console.log('-----------------');
    });
});
```

