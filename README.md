#define signalToRelayPin 13
#define sensorPin 2

int lastSoundValue;
int soundValue;
long lastNoiseTime = 0;
long currentNoiseTime = 0;
long lastLightChange = 0;
int relayStatus = LOW;;

void setup() {
  Serial.begin(9600);
  pinMode(sensorPin, INPUT);
  pinMode(signalToRelayPin, OUTPUT);
  digitalWrite(signalToRelayPin, HIGH);

  
}

void loop() {
  soundValue = digitalRead(sensorPin);
  
  currentNoiseTime = millis();

  if (soundValue == 0) { // if there is currently a noise

    if (
      (currentNoiseTime > lastNoiseTime + 200) && // to debounce a sound occurring in more than a loop cycle as a single noise
      (lastSoundValue == 1) &&  // if it was silent before
      (currentNoiseTime < lastNoiseTime + 800) && // if current clap is less than 0.8 seconds after the first clap
      (currentNoiseTime > lastLightChange + 1000) // to avoid taking a third clap as part of a pattern
    ) {

      relayStatus = !relayStatus;
      digitalWrite(signalToRelayPin, relayStatus);
      lastLightChange = currentNoiseTime;
     }

     lastNoiseTime = currentNoiseTime;
  
  }

  lastSoundValue = soundValue;

}
