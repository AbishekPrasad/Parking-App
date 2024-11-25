# Parking-App
Parking Management Application

Features
Define Parking Slots: Set the number of parking slots.
Reserve/Unreserve Slots: Reserve a slot or mark it as available.
Real-time UI Updates: Show the current state of each parking slot.
Steps to Build
1. Set Up React Native Project
Use the npx react-native init ParkingManagementApp command to create a project.
Install required libraries:
bash
Copy code
npm install react-navigation react-native-vector-icons @react-navigation/native react-native-gesture-handler react-native-reanimated react-native-screens react-native-safe-area-context
2. Define the App Components
Home Screen: Input the total number of slots.
Parking Screen: Manage reservations and display slot status.
Code Example
App Entry Point (App.js)
javascript
Copy code
import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';
import HomeScreen from './src/HomeScreen';
import ParkingScreen from './src/ParkingScreen';

const Stack = createStackNavigator();

const App = () => {
  return (
    <NavigationContainer>
      <Stack.Navigator initialRouteName="Home">
        <Stack.Screen name="Home" component={HomeScreen} />
        <Stack.Screen name="Parking" component={ParkingScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
};

export default App;
Home Screen (src/HomeScreen.js)
javascript
Copy code
import React, { useState } from 'react';
import { View, Text, TextInput, Button, StyleSheet } from 'react-native';

const HomeScreen = ({ navigation }) => {
  const [slots, setSlots] = useState('');

  const handleStart = () => {
    if (!isNaN(slots) && slots > 0) {
      navigation.navigate('Parking', { totalSlots: parseInt(slots, 10) });
    } else {
      alert('Please enter a valid number of slots!');
    }
  };

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Parking Management</Text>
      <TextInput
        style={styles.input}
        placeholder="Enter number of slots"
        keyboardType="numeric"
        value={slots}
        onChangeText={setSlots}
      />
      <Button title="Start Managing" onPress={handleStart} />
    </View>
  );
};

const styles = StyleSheet.create({
  container: { flex: 1, justifyContent: 'center', alignItems: 'center' },
  title: { fontSize: 24, marginBottom: 20 },
  input: { borderWidth: 1, width: '80%', marginBottom: 20, padding: 10 },
});

export default HomeScreen;
Parking Screen (src/ParkingScreen.js)
javascript
Copy code
import React, { useState } from 'react';
import { View, Text, TouchableOpacity, FlatList, StyleSheet } from 'react-native';

const ParkingScreen = ({ route }) => {
  const { totalSlots } = route.params;
  const [slots, setSlots] = useState(
    Array.from({ length: totalSlots }, (_, index) => ({
      id: index + 1,
      reserved: false,
    }))
  );

  const toggleSlot = (id) => {
    setSlots((prevSlots) =>
      prevSlots.map((slot) =>
        slot.id === id ? { ...slot, reserved: !slot.reserved } : slot
      )
    );
  };

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Parking Slots</Text>
      <FlatList
        data={slots}
        keyExtractor={(item) => item.id.toString()}
        numColumns={3}
        renderItem={({ item }) => (
          <TouchableOpacity
            style={[
              styles.slot,
              { backgroundColor: item.reserved ? 'red' : 'green' },
            ]}
            onPress={() => toggleSlot(item.id)}
          >
            <Text style={styles.slotText}>
              {item.reserved ? 'Reserved' : `Slot ${item.id}`}
            </Text>
          </TouchableOpacity>
        )}
      />
    </View>
  );
};

const styles = StyleSheet.create({
  container: { flex: 1, padding: 20 },
  title: { fontSize: 24, marginBottom: 20, textAlign: 'center' },
  slot: {
    flex: 1,
    margin: 5,
    height: 50,
    justifyContent: 'center',
    alignItems: 'center',
    borderRadius: 5,
  },
  slotText: { color: 'white', fontWeight: 'bold' },
});

export default ParkingScreen;
Key Functionalities
Dynamic Slot Handling: Slots are generated based on user input.
Reserve/Unreserve Toggle: Slot status changes when clicked.
State Management: Local state using React's useState.
Run the App
Start the React Native app:
bash
Copy code
npx react-native run-android
or
bash
Copy code
npx react-native run-ios
This app provides a basic parking management system. You can enhance it further by:

Adding user authentication.
Integrating a backend (e.g., Firebase) to store reservations.
Displaying analytics or usage statistics.
