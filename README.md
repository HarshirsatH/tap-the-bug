import 'dart:async';
import 'dart:math';
import 'package:flutter/material.dart';

void main() {
  runApp(TapTheBugGame());
}

class TapTheBugGame extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Tap the Bug',
      theme: ThemeData(primarySwatch: Colors.green),
      home: GameHomePage(),
      debugShowCheckedModeBanner: false,
    );
  }
}

class GameHomePage extends StatefulWidget {
  @override
  _GameHomePageState createState() => _GameHomePageState();
}

class _GameHomePageState extends State<GameHomePage> {
  bool _gameStarted = false;
  int _score = 0;
  double _bugX = 100;
  double _bugY = 100;
  final Random _random = Random();
  Timer? _timer;
  int _timeLeft = 30;

  void _startGame() {
    setState(() {
      _score = 0;
      _timeLeft = 30;
      _gameStarted = true;
      _moveBug();
    });

    _timer = Timer.periodic(Duration(seconds: 1), (timer) {
      if (_timeLeft > 0) {
        setState(() {
          _timeLeft--;
        });
      } else {
        _endGame();
      }
    });
  }

  void _endGame() {
    _timer?.cancel();
    setState(() {
      _gameStarted = false;
    });

    showDialog(
      context: context,
      builder: (_) => AlertDialog(
        title: Text('Game Over!'),
        content: Text('Your score: $_score'),
        actions: [
          TextButton(
            onPressed: () {
              Navigator.pop(context); // Close dialog
            },
            child: Text('OK'),
          ),
        ],
      ),
    );
  }

  void _moveBug() {
    final screenWidth = MediaQuery.of(context).size.width;
    final screenHeight = MediaQuery.of(context).size.height;

    setState(() {
      _bugX = _random.nextDouble() * (screenWidth - 50);
      _bugY = _random.nextDouble() * (screenHeight - 200);
    });
  }

  void _onBugTapped() {
    setState(() {
      _score++;
    });
    _moveBug();
  }

  @override
  void dispose() {
    _timer?.cancel();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Tap the Bug'),
      ),
      body: _gameStarted
          ? Stack(
        children: [
          Positioned(
            top: 20,
            left: 20,
            child: Text(
              'Score: $_score',
              style: TextStyle(fontSize: 24, fontWeight: FontWeight.bold),
            ),
          ),
          Positioned(
            top: 20,
            right: 20,
            child: Text(
              'Time: $_timeLeft',
              style: TextStyle(fontSize: 24, fontWeight: FontWeight.bold),
            ),
          ),
          Positioned(
            left: _bugX,
            top: _bugY,
            child: GestureDetector(
              onTap: _onBugTapped,
              child: Container(
                width: 50,
                height: 50,
                decoration: BoxDecoration(
                  color: Colors.red,
                  borderRadius: BorderRadius.circular(25),
                ),
              ),
            ),
          ),
        ],
      )
          : Center(
        child: ElevatedButton(
          onPressed: _startGame,
          child: Text('Start Game'),
        ),
      ),
    );
  }
}
