# Parking-App
Parking Management Application

Creating a complete codebase for a Parking Slot Management Application in React Native involves several files and components. Here's a structured guide to help you set up this app with authentication, parking slot management, and real-time updates.
Prerequisites
1.	React Native installed (npm install -g react-native-cli)
2.	Firebase setup for authentication and database management
3.	Basic understanding of React Native and Firebase.
Project Structure
markdown
Copy code
parking-app/
├── App.js
├── firebase.js
├── screens/
│   ├── LoginScreen.js
│   ├── HomeScreen.js
│   ├── AdminPanel.js
│   └── ParkingSpot.js
└── components/
    ├── ParkingSlot.js
Firebase Setup
Create a Firebase project and add your app's configuration. Then initialize Firebase in firebase.js.
firebase.js
javascript
Copy code
import firebase from 'firebase/app';
import 'firebase/auth';
import 'firebase/firestore';

const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_AUTH_DOMAIN",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_STORAGE_BUCKET",
  messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
  appId: "YOUR_APP_ID"
};

if (!firebase.apps.length) {
  firebase.initializeApp(firebaseConfig);
}

export const auth = firebase.auth();
export const db = firebase.firestore();
App.js
This file handles navigation between screens.
javascript
Copy code
import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';
import LoginScreen from './screens/LoginScreen';
import HomeScreen from './screens/HomeScreen';
import AdminPanel from './screens/AdminPanel';

const Stack = createStackNavigator();

export default function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator>
        <Stack.Screen name="Login" component={LoginScreen} />
        <Stack.Screen name="Home" component={HomeScreen} />
        <Stack.Screen name="AdminPanel" component={AdminPanel} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}
LoginScreen.js
Handles login functionality.
javascript
Copy code
import React, { useState } from 'react';
import { Button, TextInput, View, Text } from 'react-native';
import { auth } from '../firebase';

export default function LoginScreen({ navigation }) {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');

  const handleLogin = () => {
    auth.signInWithEmailAndPassword(email, password)
      .then(() => navigation.navigate('Home'))
      .catch(error => alert(error.message));
  };

  return (
    <View>
      <TextInput placeholder="Email" onChangeText={setEmail} value={email} />
      <TextInput placeholder="Password" onChangeText={setPassword} value={password} secureTextEntry />
      <Button title="Login" onPress={handleLogin} />
    </View>
  );
}
HomeScreen.js
Displays the status of all parking slots.
javascript
Copy code
import React, { useEffect, useState } from 'react';
import { View, FlatList, Text } from 'react-native';
import { db } from '../firebase';
import ParkingSlot from '../components/ParkingSlot';

export default function HomeScreen() {
  const [slots, setSlots] = useState([]);

  useEffect(() => {
    const unsubscribe = db.collection('parkingSlots').onSnapshot(snapshot => {
      setSlots(snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() })));
    });
    return unsubscribe;
  }, []);

  return (
    <View>
      <FlatList
        data={slots}
        renderItem={({ item }) => <ParkingSlot slot={item} />}
        keyExtractor={(item) => item.id}
      />
    </View>
  );
}
AdminPanel.js
Enables admins to manage parking slots.
javascript
Copy code
import React, { useState } from 'react';
import { View, Button, TextInput, Text } from 'react-native';
import { db } from '../firebase';

export default function AdminPanel() {
  const [slotNumber, setSlotNumber] = useState('');
  const [status, setStatus] = useState('');

  const addSlot = () => {
    db.collection('parkingSlots').add({ slotNumber, status });
    setSlotNumber('');
    setStatus('');
  };

  return (
    <View>
      <TextInput placeholder="Slot Number" value={slotNumber} onChangeText={setSlotNumber} />
      <TextInput placeholder="Status" value={status} onChangeText={setStatus} />
      <Button title="Add Slot" onPress={addSlot} />
    </View>
  );
}
ParkingSpot.js (Individual Parking Spot Component)
javascript
Copy code
import React from 'react';
import { View, Text, Button } from 'react-native';
import { db } from '../firebase';

export default function ParkingSpot({ slot }) {
  const reserveSlot = () => {
    db.collection('parkingSlots').doc(slot.id).update({ status: 'Reserved' });
  };

  return (
    <View>
      <Text>Slot Number: {slot.slotNumber}</Text>
      <Text>Status: {slot.status}</Text>
      <Button title="Reserve" onPress={reserveSlot} disabled={slot.status !== 'Available'} />
    </View>
  );
}
Firebase Database Structure
In Firebase Firestore, create a collection called parkingSlots with documents containing:
•	slotNumber: unique identifier for the parking slot
•	status: availability of the slot (e.g., Available, Reserved, Occupied)
Deploying to Android/iOS
Android
1.	Generate APK:
bash
Copy code
cd android
./gradlew assembleRelease
2.	Find APK at android/app/build/outputs/apk/release/app-release.apk.
iOS
1.	Open the project in Xcode.
2.	Select a target device and go to Product > Archive to create an .ipa file.
Notes
•	Authentication: Ensure only authorized admins can access the AdminPanel by checking user roles.
•	Real-time Updates: The onSnapshot function ensures real-time slot updates from Firestore.
•	Styling: Use StyleSheet in each screen for better UI.
This setup provides a complete code structure for your Parking Slot Management Application with React Native and Firebase integration. Let me know if you'd like additional help!

