# 05 組合Collections-回傳一個物件

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
function readData(stuNo){    
    return new Promise(function(resolve, reject){
        myDB.ref('/students/' + stuNo).once('value').then(studentsSnap => {
            myDB.ref('/scores/' + stuNo).once('value').then(scoresSnap => {
                if (!scoresSnap.exists() || !studentsSnap.exists()) {                    
                    resolve({});  //回傳空資料
                }else{
                    //-------------------------
                    // 取出students快照內容
                    //-------------------------
                    results={};       
                    results.stuNo = stuNo;
                    results.name = studentsSnap.val().name;
                    results.gender = studentsSnap.val().gender;
                    results.school = studentsSnap.val().school;					
                    results.type = studentsSnap.val().type;

                    //-------------------------
                    // 取出scores快照內容
                    //-------------------------					
                    scores=[];
							
                    scoresSnap.forEach(childSnap => {
                        var obj={};
                        key = childSnap.key;
                        var value = childSnap.val();
                        obj[key] = value;
										
                        scores.push(obj);
                    });	
					
                    //-------------------------
                    // 回傳資料
                    //-------------------------						
                    results.scores = scores;                  
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
readData('120001').then(results => {
    console.log(results.stuNo);
    console.log(results.name);
    console.log(results.gender);
    console.log(results.type);
    console.log(results.school);
    console.log(results.scores);	
});
```

