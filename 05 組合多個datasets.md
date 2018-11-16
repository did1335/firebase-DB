# 05 組合多個Collections

## (1) 組合Dataset的students及scores二個Collections

```
<測試>
   |__ index.js
   |
   |__ <lib>
         |__ db.js  
```

### (1-1) 資料庫的規則db.js

```javascript
{
    /* Visit https://firebase.google.com/docs/database/security to learn more about security rules. */
    "rules": {
        ".read": true,
        ".write": true,
        "scores": {
           ".indexOn": ["國文"]
        } 
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
        myDB.ref('/students/' + stuNo).once('value', (studentsSnap) => {
            myDB.ref('/scores/' + stuNo).once('value', (scoresSnap) => {
                if (!scoresSnap.exists() || !studentsSnap.exists()) {
                    //回傳空資料
                    resolve({});
                }else{
                    results={};       
                    results.stuNo = stuNo;
                    results.stuName = studentsSnap.val().name;
                    results.gender = studentsSnap.val().gender;
						
                    scores=[];
					
                    scoresSnap.forEach(childSnap => {
                        var obj={};
                        key = childSnap.key;
                        var value = childSnap.val();
                        obj[key] = value;
								
                        scores.push(obj);
                    });	
					
                    results.scores = scores;
					
                    //回傳資料
                    resolve(results);					
                }				
            });
        });       
    });
}


//----------------------------
// 讀入資料
//----------------------------
readData('120002')
    .then(results => {
        console.log(results);		
    })
    .catch(error => {
        console.log('執行錯誤! '+error);
    });
```

