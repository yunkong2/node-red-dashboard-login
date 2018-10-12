# Node-RED-Dashboard : Log-In, Session Management

Node-RED Dashboard Example with Sign In (Log In) Form, Session Management (15 secs for this example)

#### Accounts 

```
 [{ username : 'admin', password : 'admin' },
  { username : 'guest', password : 'guest' }]
```    

#### Sensor Flow 

The Sensor Flow (and modified for this demo) is from https://diyprojects.io/node-red-dashboard-gauges-charts-notifications-html

#### `ui-control`

  
- Transition from `Signin` to `SensorData` & `History` Group.  ( `TabName`_`GroupName` )

 > `{group:{hide:["Dashboard_Signin"], show:["Dashboard_SensorData", "Dashboard_History"], focus:true}} `

- Transition from `SensorData` & `History` Group to `Signin` Group.

 > `{group:{hide:["Dashboard_SensorData","Dashboard_history"], show:["Dashboard_Signin"], focus:true}}`

<p align="center">
<img src="https://github.com/phyunsj/node-red-dashboard-login/blob/master/node-red-dashboard-session-layout.png" width="300px"/>
</p>

## In Action 


<p align="center">
<img src="https://github.com/phyunsj/node-red-dashboard-login/blob/master/node-red-dashboard-session-action.gif" width="600px"/>
</p>

## Flow

<p align="center">
<img src="https://github.com/phyunsj/node-red-dashboard-login/blob/master/node-red-dashboard-session-flow.png" width="600px"/>
</p>

```
[{"id":"b89692f.2818a7","type":"ui_form","z":"12a1b8f7.edcdb7","name":"Dashboard Sign-In","label":"","group":"4dda6590.4ca25c","order":0,"width":0,"height":0,"options":[{"label":"username","value":"username","type":"text","required":true},{"label":"password","value":"password","type":"password","required":true}],"formValue":{"username":"","password":""},"payload":"","submit":"submit","cancel":"cancel","topic":"","x":106.5,"y":114,"wires":[["2a4de911.37c606","574a3fba.d3df2"]]},{"id":"466292da.f7b10c","type":"debug","z":"12a1b8f7.edcdb7","name":"Debug Account Log ","active":false,"tosidebar":true,"console":false,"tostatus":false,"complete":"payload","x":492.5,"y":76,"wires":[]},{"id":"561986e6.30d448","type":"comment","z":"12a1b8f7.edcdb7","name":"Sign-In Section","info":"","x":98.5,"y":51,"wires":[]},{"id":"2a4de911.37c606","type":"function","z":"12a1b8f7.edcdb7","name":"Account Log","func":"var accountlog =  flow.get(\"accountlog\") || [] ; \n\naccountlog.push({ accessAt : new Date(), username : msg.payload.username})\n\nflow.set(\"accountlog\", accountlog);\n\nmsg.payload = accountlog;\nreturn msg;","outputs":1,"noerr":0,"x":297.5,"y":78,"wires":[["466292da.f7b10c"]]},{"id":"574a3fba.d3df2","type":"function","z":"12a1b8f7.edcdb7","name":"Account Verification","func":"var accounts =  flow.get(\"accounts\") || [ { username : \"admin\", password : \"admin\"},{ username : \"guest\", password : \"guest\"}] ; \n\nvar username = msg.payload.username ;\nvar password = msg.payload.password ; \n\nvar newMsg = {}; newMsg.payload = 1;\n\naccounts.forEach(function ( account ){\n    if ( account.username == username ) {\n       newMsg.payload = 2;\n       if ( account.password == password ) {\n           newMsg.payload = 0;\n       } \n    }\n});\n\nreturn newMsg;","outputs":1,"noerr":0,"x":321.5,"y":148,"wires":[["ec29670c.74e618"]]},{"id":"ec29670c.74e618","type":"switch","z":"12a1b8f7.edcdb7","name":"","property":"payload","propertyType":"msg","rules":[{"t":"eq","v":"0","vt":"str"},{"t":"eq","v":"1","vt":"str"},{"t":"eq","v":"2","vt":"str"}],"checkall":"true","repair":false,"outputs":3,"x":489.5,"y":150,"wires":[["ef8bfe45.16e5c"],["c96b9e17.b040c"],["ee466dd6.fe058"]]},{"id":"ef8bfe45.16e5c","type":"function","z":"12a1b8f7.edcdb7","name":"= 0 : Success","func":"/* activate session timer */\nvar sessionTimer =  flow.get(\"sessionTimer\") || 0; \nvar currTime = Date.now();\nflow.set(\"sessionTimer\", currTime);\n\n/* ui-control payload */\nvar msg = {};\nmsg.payload = { group: {\n    show : [\"Dashboard_SensorData\", \"Dashboard_History\"],\n    hide : [\"Dashboard_Signin\"]\n}\n};\n\nreturn msg;","outputs":1,"noerr":0,"x":649.5,"y":116,"wires":[["a4ccc52d.c8f328"]]},{"id":"c96b9e17.b040c","type":"function","z":"12a1b8f7.edcdb7","name":"= 1 : Unknown User","func":"msg.payload = \"Username Doesn't exist!!!\"\nreturn msg;","outputs":1,"noerr":0,"x":669.5,"y":155,"wires":[["e8e1dd24.9f3ed"]]},{"id":"ee466dd6.fe058","type":"function","z":"12a1b8f7.edcdb7","name":"= 2 : Wrong Password","func":"msg.payload = \"Invalid Password\";\nreturn msg;","outputs":1,"noerr":0,"x":672.5,"y":194,"wires":[["e8e1dd24.9f3ed"]]},{"id":"a4ccc52d.c8f328","type":"ui_ui_control","z":"12a1b8f7.edcdb7","name":"Go to Sensor/History Group","x":938.5,"y":115,"wires":[[]]},{"id":"e8e1dd24.9f3ed","type":"ui_toast","z":"12a1b8f7.edcdb7","position":"dialog","displayTime":"3","highlight":"","outputs":1,"ok":"OK","cancel":"","topic":"Authentication Failed","name":"Authentication Failure Dialog","x":939.5,"y":176,"wires":[[]]},{"id":"ac43b826.84ca38","type":"comment","z":"12a1b8f7.edcdb7","name":"Session Management - 15 secs for this demo","info":"","x":188,"y":234.00000667572021,"wires":[]},{"id":"215df146.1e462e","type":"inject","z":"12a1b8f7.edcdb7","name":"Timer","topic":"","payload":"","payloadType":"date","repeat":"3","crontab":"","once":false,"onceDelay":0.1,"x":88.49999618530273,"y":337.0000057220459,"wires":[["e7f6cd3b.3f36c","b4cf366.4befdc8"]]},{"id":"e7f6cd3b.3f36c","type":"function","z":"12a1b8f7.edcdb7","name":"Detect Session Timer","func":"var sessionTimer =  flow.get(\"sessionTimer\") || 0; \nvar currTime = Date.now();\nvar SESSION_TIMEOUT = 15000; //15 secs\n\nif ( sessionTimer === 0 /* Inactive, No user signed in */ ) {\n    msg.payload = 2;\n} else {\n    if ( currTime - sessionTimer > SESSION_TIMEOUT ) {\n      msg.payload = 0;  \n    } else {\n      msg.payload = 1;        \n    }\n}\nreturn msg;","outputs":1,"noerr":0,"x":272.5,"y":298.99999809265137,"wires":[["9309e91a.e488f8"]]},{"id":"9309e91a.e488f8","type":"switch","z":"12a1b8f7.edcdb7","name":"","property":"payload","propertyType":"msg","rules":[{"t":"eq","v":"0","vt":"str"},{"t":"eq","v":"1","vt":"str"},{"t":"eq","v":"2","vt":"str"}],"checkall":"true","repair":false,"outputs":3,"x":446.50000381469727,"y":298.99999809265137,"wires":[["ac9c6ee6.03b42"],["fda0446a.36ac28"],["55e8bd2b.b91784"]]},{"id":"ac9c6ee6.03b42","type":"function","z":"12a1b8f7.edcdb7","name":"Time-Out","func":"/* activate session timer */\nvar sessionTimer =  flow.get(\"sessionTimer\") || 0; \nflow.set(\"sessionTimer\", 0);\n\nmsg.payload = \"Session Expired! Please singin again!\";\nreturn msg;","outputs":1,"noerr":0,"x":586.5,"y":259,"wires":[["ae766df1.29561","7c68531e.1ced1c"]]},{"id":"ae766df1.29561","type":"ui_toast","z":"12a1b8f7.edcdb7","position":"dialog","displayTime":"3","highlight":"","outputs":1,"ok":"OK","cancel":"","topic":"Session Timeout","name":"Time-Out Dialog","x":776.0000457763672,"y":236.00000953674316,"wires":[[]]},{"id":"fbb315d8.828908","type":"ui_ui_control","z":"12a1b8f7.edcdb7","name":"Go to Signin Group","x":984.0000190734863,"y":276.99999713897705,"wires":[[]]},{"id":"7c68531e.1ced1c","type":"function","z":"12a1b8f7.edcdb7","name":"Sign-In Redirection","func":"var msg = {};\nmsg.payload = { group: {\n    hide : [\"Dashboard_SensorData\", \"Dashboard_History\"],\n    show : [\"Dashboard_Signin\"]\n}\n};\nreturn msg;","outputs":1,"noerr":0,"x":786.5000457763672,"y":277.00000286102295,"wires":[["fbb315d8.828908"]]},{"id":"6e77a342.6b010c","type":"ui_toast","z":"12a1b8f7.edcdb7","position":"bottom right","displayTime":"1","highlight":"","outputs":0,"ok":"OK","cancel":"","topic":"Timer","name":"Display Remaining Time","x":602.9999923706055,"y":400.99999809265137,"wires":[]},{"id":"47481dd0.83e404","type":"comment","z":"12a1b8f7.edcdb7","name":"Sensor Data Collection","info":"","x":117.5,"y":452.0000057220459,"wires":[]},{"id":"561782d2.e287cc","type":"inject","z":"12a1b8f7.edcdb7","name":"Timer","topic":"","payload":"","payloadType":"date","repeat":"2","crontab":"","once":false,"onceDelay":0.1,"x":89.99999237060547,"y":529.0000133514404,"wires":[["f811a1d4.f7c0d"]]},{"id":"a536c3aa.58a11","type":"comment","z":"12a1b8f7.edcdb7","name":"MQTT ? CoAP?","info":"Expecting data from the actaul sensors.","x":265,"y":488.99999809265137,"wires":[]},{"id":"b4cf366.4befdc8","type":"function","z":"12a1b8f7.edcdb7","name":"Calculating Remaining Time (Debug)","func":"var sessionTimer =  flow.get(\"sessionTimer\") || 0; \nvar currTime = Date.now();\nvar remainingSecs = Math.floor((15000 - (currTime - sessionTimer))/1000) + 1;\n    \nif ( sessionTimer == 0 /* Inactive, No user signed in */ ) {\n    return null;\n} else {\n    \n    if ( remainingSecs <= 7 ) msg.payload = \"Remaining : \" + remainingSecs + \" secs\";  \n    else return null;\n}\nreturn msg;","outputs":1,"noerr":0,"x":320.5,"y":400.00001335144043,"wires":[["6e77a342.6b010c"]]},{"id":"5b1e9c02.b32ec4","type":"ui_text","z":"12a1b8f7.edcdb7","group":"1c73c43f.d461fc","order":0,"width":0,"height":0,"name":"Label Display","label":"","format":"{{msg.payload}}","layout":"row-spread","x":1003.0000076293945,"y":612.0001049041748,"wires":[]},{"id":"f811a1d4.f7c0d","type":"function","z":"12a1b8f7.edcdb7","name":"Decode MySensor Message","func":"\nvar mySensorsMessage = {}\nvar newPayload = {};\nvar timestamp = new Date();\n\nnewPayload.nodeId  =  3;\nnewPayload.type =  Math.floor( Math.random() * Math.floor(2));  // 0 or 1\nif ( newPayload.type === 0 /* temperature */ ) newPayload.value   =  Math.floor( Math.random() * Math.floor(30)) + 10; // 10 - 39\nelse newPayload.value   =  Math.floor( Math.random() * Math.floor(40)) + 40; // 40 - 79\n\nmsg.payload = newPayload; \n\nreturn msg;","outputs":1,"noerr":0,"x":286,"y":530.000020980835,"wires":[["d2b9a96.3eb6058","1a884f9f.2d46"]]},{"id":"d2b9a96.3eb6058","type":"function","z":"12a1b8f7.edcdb7","name":"Filter : temperature","func":"if (msg.payload.nodeId == 3 && msg.payload.type === 0) {\n    var msg;\n    msg.payload = msg.payload.value;\n    msg.topic = \"Temperature\"\n    return msg;\n}    ","outputs":1,"noerr":0,"x":491.0000762939453,"y":474.0000066757202,"wires":[["fb435430.ade1a8","29fc8f0f.de3fc","93bea02d.2dae"]]},{"id":"fb435430.ade1a8","type":"ui_gauge","z":"12a1b8f7.edcdb7","name":"Gauge : Temperature","group":"1c73c43f.d461fc","order":0,"width":0,"height":0,"gtype":"gage","title":"Temperature DHT22","label":"MySensors v2","format":"{{value}}°C","min":0,"max":"50","colors":["#66ccff","#ff8000","#ca3838"],"seg1":"","seg2":"","x":754.0000228881836,"y":467.99998664855957,"wires":[]},{"id":"1a884f9f.2d46","type":"function","z":"12a1b8f7.edcdb7","name":"Filter : humidity","func":"if (msg.payload.nodeId == 3 && msg.payload.type === 1) {\n    var msg;\n    msg.payload = msg.payload.value;\n    msg.topic = \"Humidity\"\n    return msg;\n}    ","outputs":1,"noerr":0,"x":478.00008392333984,"y":592.0000095367432,"wires":[["adec7ade.484ec8","29fc8f0f.de3fc","93bea02d.2dae"]]},{"id":"adec7ade.484ec8","type":"ui_gauge","z":"12a1b8f7.edcdb7","name":"Gauge : Humidity","group":"1c73c43f.d461fc","order":0,"width":"0","height":"0","gtype":"gage","title":"Humidity DHT22","label":"MySensors v2","format":"{{value}}%","min":0,"max":"100","colors":["#1a25ab","#e6e600","#ca3838"],"seg1":"","seg2":"","x":745.0000648498535,"y":509.00003242492676,"wires":[]},{"id":"29fc8f0f.de3fc","type":"function","z":"12a1b8f7.edcdb7","name":"Attention : temperature & humidity","func":"context.data = context.data || new Object();\n\nswitch (msg.topic) {\n    case \"Temperature\":\n        context.data.temperature = msg.payload;\n        msg = null;\n        break;\n    case \"Humidity\":\n        context.data.humidity = msg.payload;\n        msg = null;\n        break;\n    default:\n        msg = null;\n    \tbreak;\n\n}\n\nif(context.data.temperature !== null && context.data.humidity !== null && context.data.temperature !== undefined && context.data.humidity !== undefined) {\n\tmsgOut = \"\"; //new Object();\n    msgOut = \"Temerature : \" \n    msgOut += context.data.temperature.toString(); \n    msgOut+= \"°C - Humidity : \"; \n    msgOut+= context.data.humidity.toString();\n    msgOut+= \"%\";\n    context.data=null;\n    var msg = {};\n    msg.payload = msgOut;\n\treturn msg;\n} \n//else return msg;","outputs":1,"noerr":0,"x":766.9999694824219,"y":612.0000381469727,"wires":[["5b1e9c02.b32ec4"]]},{"id":"93bea02d.2dae","type":"ui_chart","z":"12a1b8f7.edcdb7","name":"History : Temperature & Humidity","group":"7bb6904.00fcd7","order":0,"width":"6","height":"9","label":"Temperature & Humidity","chartType":"line","legend":"true","xformat":"%H:%M","interpolate":"linear","nodata":"No Data Yet","dot":false,"ymin":"0","ymax":"80","removeOlder":"12","removeOlderPoints":"","removeOlderUnit":"3600","cutout":"","useOneColor":false,"colors":["#1f77b4","#aec7e8","#ff7f0e","#2ca02c","#98df8a","#d62728","#ff9896","#9467bd","#c5b0d5"],"useOldStyle":true,"x":795.0000495910645,"y":550.0000829696655,"wires":[[],[]]},{"id":"1c42befd.671181","type":"ui_ui_control","z":"12a1b8f7.edcdb7","name":"Stay in Sensor Group","x":972.9999694824219,"y":314.99999237060547,"wires":[[]]},{"id":"fda0446a.36ac28","type":"function","z":"12a1b8f7.edcdb7","name":"Sensor/History Goup","func":"var msg = {};\nmsg.payload = { group: {\n    show : [\"Dashboard_SensorData\", \"Dashboard_History\"],\n    hide : [\"Dashboard_Signin\"]\n}\n};\nreturn msg;","outputs":1,"noerr":0,"x":661,"y":319.9999942779541,"wires":[["1c42befd.671181"]]},{"id":"55e8bd2b.b91784","type":"function","z":"12a1b8f7.edcdb7","name":"Signin Goup","func":"var msg = {};\nmsg.payload = { group: {\n    hide : [\"Dashboard_SensorData\", \"Dashboard_History\"],\n    show : [\"Dashboard_Signin\"]\n}\n};\nreturn msg;","outputs":1,"noerr":0,"x":638.0000228881836,"y":360.0000219345093,"wires":[["e714728e.44ef8"]]},{"id":"e714728e.44ef8","type":"ui_ui_control","z":"12a1b8f7.edcdb7","name":"Go to Signin Group","x":978.0000762939453,"y":356.0000343322754,"wires":[[]]},{"id":"4dda6590.4ca25c","type":"ui_group","z":"","name":"Signin","tab":"7d44c889.f36c58","order":1,"disp":true,"width":"6","collapse":false},{"id":"1c73c43f.d461fc","type":"ui_group","z":"","name":"SensorData","tab":"7d44c889.f36c58","order":2,"disp":true,"width":"6","collapse":false},{"id":"7bb6904.00fcd7","type":"ui_group","z":"","name":"History","tab":"7d44c889.f36c58","order":3,"disp":true,"width":"6","collapse":false},{"id":"7d44c889.f36c58","type":"ui_tab","z":"","name":"Dashboard","icon":"dashboard","order":1}]
```

## Additional Development

- HTTPS Setup (https://www.hardill.me.uk/wordpress/2015/05/11/securing-node-red/) 
- New User Registration
- Database : account, log, etc

