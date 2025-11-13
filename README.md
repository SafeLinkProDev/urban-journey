REDAME.md
# SafeLinkPro-Beta

# SafeLinkPro – Prototype Officiel (Beta)

Application mobile de prévention des accidents, alertes vocales intelligentes, fonctionnement hors-ligne, détection des écouteurs et géolocalisation automatique.

## Fonctionnalités principales
- Mode conduite sécurisé (alertes vocales).
- Détection automatique des écouteurs.
- Fonctionnement hors-ligne + cloud.
- Système d’abonnement intégré.
- Géolocalisation automatique en cas d’alerte.
- Conversion texte → voix et voix → texte.
- Module de sécurité (chiffrement, confidentialité).
- Interface optimisée pour conducteurs, ouvriers, agents de sécurité et interventions.

## Architecture du projet
- **Flutter** (mobile cross-platform).
- **Modules IA** (locaux et cloud).
- **Gestion hors-ligne** pour zones sans réseau.
- **Connexion Bluetooth** pour écouteurs et casques.
- **Sécurité & chiffrement** intégrés.

## Installation (développeurs)
1. Cloner le dépôt :  
   `git clone https://github.com/SafeLinkPro-Beta/SafeLinkPro`
2. Installer les dépendances :  
   `flutter pub get`
3. Lancer l’application :  
   `flutter run`

## Roadmap (étapes à venir)
- Intégration complète du Mode Conducteur.
- Maquette UI/UX dynamique.
- Système d’abonnement finalisé.
- Test vocal mains-libres.
- Tableau de bord des superviseurs.
- Génération vocale multilingue (selon langue du téléphone).

## A propos
SafeLinkPro est une solution technologique dédiée à la sécurité routière, industrielle, communautaire et professionnelle.  
Objectif : réduire les accidents, faciliter les alertes et protéger les utilisateurs sur le terrain.
Mise à jour du README.md


assets/.gitkeep
assets/icons/.gitkeep
assets/audio/.gitkeep
assets/images/.gitkeep
lib/main.dart
import 'package:flutter/material.dart';

void main() {
  runApp(const SafeLinkProApp());
}

class SafeLinkProApp extends StatelessWidget {
  const SafeLinkProApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'SafeLinkPro',
      theme: ThemeData(
        primarySwatch: Colors.blue,
        useMaterial3: true,
      ),
      home: const HomePage(),
    );
  }
}

class HomePage extends StatelessWidget {
  const HomePage({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('SafeLinkPro – Prototype Beta'),
      ),
      body: const Center(
        child: Text(
          'Bienvenue dans SafeLinkPro Beta',
          style: TextStyle(fontSize: 20),
        ),
      ),
    );
  }
}
lib/services/speech_service.dart
import 'package:flutter_tts/flutter_tts.dart';

class SpeechService {
  final FlutterTts _tts = FlutterTts();

  SpeechService() {
    _tts.setLanguage("fr-FR");
    _tts.setPitch(1.0);
    _tts.setSpeechRate(0.5);
  }

  Future speak(String message) async {
    await _tts.speak(message);
  }

  Future stop() async {
    await _tts.stop();
  }
}
lib/services/noise_service.dart
class NoiseService {
  // Simule une détection de bruit pour le prototype
  bool isLoudEnvironment(double decibel) {
    return decibel > 70; // seuil de bruit
  }
}
lib/services/bluetooth_service.dart
class BluetoothService {
  // Simule la détection des écouteurs
  bool isHeadsetConnected = false;

  void connectHeadset() {
    isHeadsetConnected = true;
  }

  void disconnectHeadset() {
    isHeadsetConnected = false;
  }
}
lib/services/alert_service.dart
class AlertService {
  void sendAlert(String message) {
    // Prototype d'envoi d'alerte
    print("Alerte envoyée : $message");
  }
}
lib/home.dart
import 'package:flutter/material.dart';
import 'services/speech_service.dart';
import 'services/bluetooth_service.dart';
import 'services/noise_service.dart';
import 'services/alert_service.dart';

class HomeScreen extends StatefulWidget {
  const HomeScreen({super.key});

  @override
  State<HomeScreen> createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {
  final SpeechService _speechService = SpeechService();
  final BluetoothService _bluetoothService = BluetoothService();
  final NoiseService _noiseService = NoiseService();
  final AlertService _alertService = AlertService();

  bool _isNoisy = false;
  String _lastAlert = "Aucune alerte envoyée pour le moment.";

  void _activateStrictMode() {
    final message =
        "Alerte SafeLinkPro : Mode Sécurité Strict activé. Le conducteur ne peut pas répondre aux appels.";
    _alertService.sendAlert(message);
    _speechService.speak(message);
    setState(() {
      _lastAlert = "Mode Sécurité Strict activé.";
    });
  }

  void _activateSafeCommunication() {
    if (!_bluetoothService.isHeadsetConnected) {
      ScaffoldMessenger.of(context).showSnackBar(
        const SnackBar(
          content:
              Text("Aucun écouteur détecté (simulation). Connectez un casque."),
        ),
      );
      return;
    }

    final message =
        "Mode Communication Sécurisée activé. Les échanges se font via l’oreillette.";
    _alertService.sendAlert(message);
    _speechService.speak(message);
    setState(() {
      _lastAlert = "Mode Communication Sécurisée activé.";
    });
  }

  void _testVoice() {
    final message =
        "Ceci est un test vocal SafeLinkPro Urban Journey, prototype beta.";
    _speechService.speak(message);
  }

  @override
  Widget build(BuildContext context) {
    final envText = _isNoisy ? "Environnement bruyant" : "Environnement normal";

    return Scaffold(
      appBar: AppBar(
        title: const Text("SafeLinkPro – Mode Conduite (Beta)"),
      ),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.stretch,
          children: [
            const Text(
              "SafeLinkPro – Urban Journey",
              textAlign: TextAlign.center,
              style: TextStyle(
                fontSize: 22,
                fontWeight: FontWeight.bold,
              ),
            ),
            const SizedBox(height: 8),
            Text(
              envText,
              textAlign: TextAlign.center,
            ),
            const SizedBox(height: 16),

            // Simulation écouteurs
            SwitchListTile(
              title: const Text("Écouteur connecté (simulation)"),
              value: _bluetoothService.isHeadsetConnected,
              onChanged: (value) {
                setState(() {
                  if (value) {
                    _bluetoothService.connectHeadset();
                  } else {
                    _bluetoothService.disconnectHeadset();
                  }
                });
              },
            ),

            // Simulation environnement bruyant
            SwitchListTile(
              title: const Text("Environnement bruyant (simulation)"),
              value: _isNoisy,
              onChanged: (value) {
                setState(() {
                  _isNoisy = value;
                  // On pourrait utiliser _noiseService ici plus tard
                  _noiseService.isLoudEnvironment(value ? 80 : 40);
                });
              },
            ),

            const SizedBox(height: 16),

            // Bouton Mode Sécurité Strict
            ElevatedButton(
              onPressed: _activateStrictMode,
              child: const Text("Mode Sécurité Strict"),
            ),
            const SizedBox(height: 12),

            // Bouton Mode Communication Sécurisée
            ElevatedButton(
              onPressed: _activateSafeCommunication,
              child: const Text("Mode Communication Sécurisée"),
            ),
            const SizedBox(height: 12),

            // Bouton test vocal
            OutlinedButton(
              onPressed: _testVoice,
              child: const Text("Tester la voix (démo)"),
            ),

            const SizedBox(height: 24),
            const Text(
              "Dernière action :",
              style: TextStyle(fontWeight: FontWeight.bold),
            ),
            Text(_lastAlert),
          ],
        ),
      ),
    );
  }
}
import 'package:flutter/material.dart';
import 'home.dart';

void main() {
  runApp(const SafeLinkProApp());
}

class SafeLinkProApp extends StatelessWidget {
  const SafeLinkProApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'SafeLinkPro',
      theme: ThemeData(
        primarySwatch: Colors.blue,
        useMaterial3: true,
      ),
      home: const HomeScreen(),
    );
  }
}