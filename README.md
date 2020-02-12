# flutter_app_switch

A new Flutter application.

## Getting Started

This project is a starting point for a Flutter application.

A few resources to get you started if this is your first Flutter project:

- [Lab: Write your first Flutter app](https://flutter.dev/docs/get-started/codelab)
- [Cookbook: Useful Flutter samples](https://flutter.dev/docs/cookbook)

For help getting started with Flutter, view our
[online documentation](https://flutter.dev/docs), which offers tutorials,
samples, guidance on mobile development, and a full API reference.


import 'package:flutter/material.dart';
import 'dart:convert';
import 'package:adhara_socket_io/adhara_socket_io.dart';

void main() {


  runApp(new MaterialApp(
    home: new MyApp(),
  ));

}
class MyApp extends StatefulWidget {

  @override
  _State createState() => new _State();
}
class _State extends State<MyApp> {
  ///////////////////
  List<String> toPrint = ["trying to connect"];
  SocketIOManager manager;
  SocketIO sockets;
  bool _isProbablyConnected =false;
  //Map<String, SocketIO> sockets = {};
  //Map<String, bool> _isProbablyConnected = {};
  //////////////////////

  bool _value =false;
  bool _valueStatus =false;

  void _onChanged(bool value) {
    setState(() {
      _value = value;
    });
    if (_value == false) {
      kitchenOff();
    }
    if (_value == true) {
      kitchenOn();
    }

  }

  void _onChangedStatus(bool value) {
    setState(() {
      _valueStatus = value;
    });
    sendMessage();

  }

  //////////////////////
  void initState() {
    super.initState();
    manager = SocketIOManager();
    initSocket();
  }
  //////////////////////

  initSocket() async {
    const String URI = "http://139.59.210.114:8000/";
    setState(() => _isProbablyConnected = true);
    SocketIO socket = await manager.createInstance(SocketOptions(
      //Socket IO server URI
        URI,
        //nameSpace: (identifier == "namespaced")?"/adhara":"/",
        //Query params - can be used for authentication
        query: {
          "auth": "--SOME AUTH STRING---",
          "info": "new connection from adhara-socketio",
          "timestamp": DateTime.now().toString()
        },
        //Enable or disable platform channel logging
        enableLogging: false,
        transports: [Transports.WEB_SOCKET/*, Transports.POLLING*/] //Enable required transport
    ));
    socket.onConnect((data) {
      pprint("connected...");
      pprint(data);
      sendMessage();
    });
    socket.on("event", (data){   //sample event
      print("event");
      print(data);

    });

    socket.connect();
    sockets = socket;
  }

  sendMessage() {
    if (sockets != null) {
      //pprint("sending message from 'get_device_action_value'...");
      sockets.emit("get_device_action_value",["{\"device_id\": \"5de15269105cfc3cc094130f\",\"action_name\": \"setPowerState\"}"]);
      //sockets.emit("register",["{\"email\": \"amro.elalawi@gmail.com\",\"username\": \"amroelalawi\",\"password\": \"123456789\",\"mobile_number\": \"01062040596\",\"age\": \"29\",\"full_name\": \"amro mohamed elalawi\",\"country\": \"egypt\",\"city\": \"zayed\"}"]);
      //sockets.emit("register",["{\"email\": \"h.marzouk@nu.edu.eg\",\"username\": \"hagarmarzouk\",\"password\": \"123456789\",\"mobile_number\": \"01062040596\",\"age\": \"23\",\"full_name\": \"hagar marzouk omar\",\"country\": \"egypt\",\"city\": \"zayed\"}"]);
      //sockets.emit("add_device_user",["{\"username\": \"hagarmarzouk\",\"password\": \"123456789\",\"device_id\": \"5de15269105cfc3cc094130f\"}"]);
      pprint("Message emitted from app  'get_device_action_value'...");
    }
  }

  kitchenOn() {
    if (sockets != null) {
      //pprint("sending message from 'change_device_action_value_ON'...");
      sockets.emit("change_device_action_value",["{\"username\": \"amroelalawi\",\"password\": \"123456789\",\"device_id\": \"5de15269105cfc3cc094130f\",\"action_name\": \"setPowerState\" ,\"action_value\": \"ON\"}"]);

      pprint("Message emitted from app  'change_device_action_value_ON'...");
    }
  }

  kitchenOff() {
    if (sockets != null) {
      //pprint("sending message from 'change_device_action_value_OFF'...");
      sockets.emit("change_device_action_value",["{\"username\": \"amroelalawi\",\"password\": \"123456789\",\"device_id\": \"5de15269105cfc3cc094130f\",\"action_name\": \"setPowerState\" ,\"action_value\": \"OFF\"}"]);

      pprint("Message emitted from app  'change_device_action_value_OFF'...");
    }
  }


  pprint(data) {
    setState(() {
      if (data is Map) {
        data = json.encode(data);
      }
      print(data);
      toPrint.add(data);
    });
  }


  /////////////////////////




  @override
  Widget build(BuildContext context){
    return new Scaffold(
      appBar: new AppBar(
        title: new Text('Wamda-IoT Smart Home'),
      ),
      body: new Container(
        padding: new EdgeInsets.all(32.0),
        child: new Column(
          children: <Widget>[
            //new Switch(value: _valueStatus, onChanged:  (bool value){_onChanged(value);}),
            new SwitchListTile(
              title: new Text('Kitchen Status'),
                activeColor: Colors.blue,
                secondary: const Icon(Icons.home),
                value: _valueStatus,
                onChanged: (bool value){_onChangedStatus(value);}
                ),
            new SwitchListTile(
                title: new Text('Kitchen ON/OFF'),
                activeColor: Colors.green,
                secondary: const Icon(Icons.lightbulb_outline),
                value: _value,
                onChanged: (bool value){_onChanged(value);}
            ),

          ],
        ),
      ) ,
    );
  }
}