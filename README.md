# OpenDrive Control -  Direction automobile contrôlée par joystick


## Objectif

Remplacer le volant traditionnel par un **joystick proportionnel** contrôlant l’angle de braquage via un **moteur 12V**, en agissant directement sur le volant via une **roue dentée soudée**.

---

## Principe de fonctionnement

- Un **joystick analogique** (auto-centrant) donne une valeur proportionnelle à l’inclinaison du levier.
- Cette valeur est convertie en **consigne d’angle de braquage**.
- Un **moteur à courant continu 12V**, avec un **réducteur à fort couple**, entraîne une roue dentée soudée au volant.
- Un **encodeur rotatif** mesure l’angle réel du volant.
- Un **PID** régule la position du volant pour suivre l’angle cible.
- Quand le joystick revient au neutre, le système ramène le volant à **0°**.

---

## Matériel nécessaire

### Contrôle
- Joystick analogique (ex. KY-023 ou modèle Arduino)
- Microcontrôleur (Arduino Uno, Mega, ESP32...)

### Motorisation
- Moteur à courant continu 12V avec réducteur planétaire
  - Couple ≥ 10 Nm
  - Vitesse ≈ 30–90 RPM
  - Réduction 1:50 à 1:100
  - Exemple : ZGA37RG 12V 30RPM

### Capteurs et retour
- Encodeur rotatif (ex. 600 PPR)
- Lecteur d’encodeur (ex. module KY-040 ou encodeur intégré)

### Alimentation et commande
- Alimentation 12V ≥ 10 A
- Pont en H / Driver moteur :
  - BTS7960 (43A)
  - Cytron MD30C (30A)

### Mécanique
- Roue dentée soudée sur le volant
- Pignon solidaire de l’axe moteur
- Support moteur robuste

---

## Logique de contrôle (simplifiée)

```cpp
loop() {
  float joystick = readJoystick(); // -1.0 à +1.0
  float targetAngle = joystick * maxSteeringAngle; // Ex. +/- 35°
  float currentAngle = readEncoder(); // En degrés

  float error = targetAngle - currentAngle;
  float motorSpeed = PID(error);
  
  setMotor(motorSpeed);
}
```
---


# Idées qui peuvent être ajoutées au projet


## Via un écran dédié :

- Logique de contrôle du mouvement (Selon mode)

   - Précis (Chaque millimètre compte)
   - Doux (Réaction du moteur moins instantanée et beaucoup plus douce (Idéale pour l'autoroute))
   - Représentation graphique en temps réel de l'angle des roues avec une règle des de l'inclinaisons du volant

## Via un un/des boutons dédié :
- Clignotants G et D (CanBus)


# Implémenter une logique de sécuriter à chaque démarrage

-   Initialiser le système en tarant bien à 0 le centarge du volant avec le 0 du levier
-   Si le couple de braquage est supérieur à `X NM` alors mise en sécuriter et reproceder à l'étape d'initialisation !
-   Test initiale après l'initialisation d'un 100% à gauche + 0% au centre + 100% à droite ce qui nous donne une visulation comme ceci : 

>Note : ` • = Position du volant` 

| Pourcentage | Direction | Représentation               |
|-------------|-----------|------------------------------|
| 100%        | Gauche    | ¦•---------○----------¦       |
| 0%          | Centré    | ¦----------•----------¦       |
| 100%        | Droite    | ¦----------○---------•¦       |

