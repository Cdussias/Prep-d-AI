# Prep-d-AI// Prep'd AI - Starter Code v2
// To run:
// 1. Save this entire block as App.tsx in your Expo project.
// 2. Place your logo image as ./assets/prepd_logo.png
// 3. Run 'npx expo start'
// 4. Scan the QR code with your iPhone using the Expo Go app.

import React, { useState, useEffect } from 'react';
import { View, Text, Image, TouchableOpacity, SafeAreaView, StyleSheet, ActivityIndicator, ScrollView, Alert, Dimensions } from 'react-native';
import { NavigationContainer, useNavigation } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';
import { StatusBar } from 'expo-status-bar';
import { LinearGradient } from 'expo-linear-gradient';
import * as ImagePicker from 'expo-image-picker';
import { MaterialCommunityIcons } from '@expo/vector-icons';

// --- CONFIGURATION PLACEHOLDERS ---

// 1. Firebase Placeholder
// Replace these with your actual Firebase config later.
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_AUTH_DOMAIN",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_STORAGE_BUCKET",
  messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
  appId: "YOUR_APP_ID"
};

// 2. AI Key Placeholder
// Use an environment variable setup like 'expo-secure-store' or a backend service for production.
// For now, it's just a placeholder to show where the key is used.
const OPENAI_API_KEY = process.env.OPENAI_API_KEY || 'YOUR_OPENAI_API_KEY_HERE';

// --- THEME & STYLING ---

const NEON_GREEN = '#39FF14';
const NEON_BLUE = '#00FFFF';
const DARK_GRAY = '#1a1a1a';
const DEEP_BLACK = '#000000';
const LIGHT_GRAY = '#aaaaaa';

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: DARK_GRAY,
    alignItems: 'center',
    justifyContent: 'center',
  },
  logoLarge: {
    width: 200,
    height: 200,
    resizeMode: 'contain',
    marginBottom: 20,
  },
  logoSmall: {
    width: 80,
    height: 80,
    resizeMode: 'contain',
    marginBottom: 40,
    tintColor: NEON_GREEN,
  },
  banner: {
    width: 50,
    height: 50,
    resizeMode: 'contain',
    tintColor: NEON_BLUE,
    marginRight: 10,
  },
  slogan: {
    fontSize: 18,
    color: LIGHT_GRAY,
    fontStyle: 'italic',
  },
  title: {
    fontSize: 24,
    fontWeight: 'bold',
    color: NEON_GREEN,
    marginBottom: 30,
    textShadowColor: NEON_GREEN,
    textShadowRadius: 5,
  },
  button: {
    backgroundColor: NEON_BLUE,
    paddingVertical: 15,
    paddingHorizontal: 50,
    borderRadius: 8,
    elevation: 3,
  },
  buttonText: {
    color: DEEP_BLACK,
    fontSize: 18,
    fontWeight: 'bold',
  },
  floatingButton: {
    position: 'absolute',
    bottom: 50,
    width: 120,
    height: 120,
    borderRadius: 60,
    backgroundColor: 'transparent',
    alignItems: 'center',
    justifyContent: 'center',
    borderWidth: 2,
    borderColor: NEON_GREEN,
    shadowColor: NEON_GREEN,
    shadowOpacity: 1,
    shadowRadius: 15,
    elevation: 10,
  },
  floatingButtonText: {
    color: NEON_GREEN,
    fontSize: 18,
    fontWeight: 'bold',
    textShadowColor: NEON_GREEN,
    textShadowRadius: 8,
  },
  analysisCard: {
    width: '90%',
    padding: 20,
    backgroundColor: DARK_GRAY,
    borderRadius: 10,
    borderWidth: 1,
    borderColor: NEON_BLUE,
    shadowColor: NEON_BLUE,
    shadowOpacity: 0.5,
    shadowRadius: 10,
    marginBottom: 20,
  },
  analysisTitle: {
    fontSize: 20,
    fontWeight: 'bold',
    color: NEON_BLUE,
    marginBottom: 10,
    textShadowColor: NEON_BLUE,
    textShadowRadius: 5,
  },
  detailText: {
    fontSize: 16,
    color: LIGHT_GRAY,
    marginBottom: 5,
  },
  macroTitle: {
    fontSize: 18,
    fontWeight: 'bold',
    color: NEON_GREEN,
    marginTop: 10,
    marginBottom: 5,
  },
  // New styles for image preview and analysis
  imagePreview: {
    width: '90%',
    height: 300,
    borderRadius: 10,
    resizeMode: 'cover',
    marginBottom: 20,
    borderWidth: 2,
    borderColor: NEON_BLUE,
  },
  actionButton: {
    backgroundColor: NEON_GREEN,
    paddingVertical: 12,
    paddingHorizontal: 30,
    borderRadius: 5,
    marginTop: 15,
    flexDirection: 'row',
    alignItems: 'center',
    justifyContent: 'center',
  },
  actionButtonText: {
    color: DEEP_BLACK,
    fontSize: 16,
    fontWeight: 'bold',
    marginLeft: 10,
  },
  headerTitle: {
    color: NEON_GREEN,
    fontSize: 20,
    fontWeight: 'bold',
    marginLeft: 10,
  }
});

// --- FIREBASE (SIMULATED) ---
const simulateFirebaseUpload = async (imageUri) => {
  // Simulates uploading the image to Firebase Storage and getting a public URL
  await new Promise(resolve => setTimeout(resolve, 1500)); // Simulate network latency
  console.log("Simulated Firebase Storage upload:", imageUri);
  return 'https://simulated.storage.url/image.jpg'; // Return a mock URL
};

const simulateFirebaseSave = async (mealData) => {
  // Simulates saving the meal data to Firestore
  await new Promise(resolve => setTimeout(resolve, 500)); // Simulate network latency
  console.log("Simulated Firestore save:", mealData);
};

// --- AI VISION (SIMULATED) ---
const simulateAIVision = async (imageUri) => {
  // Simulates calling the OpenAI Vision API
  console.log("Simulating OpenAI Vision API call for:", imageUri);
  await new Promise(resolve => setTimeout(resolve, 3000)); // Simulate API processing time

  // Detailed breakdown as requested
  const mockAnalysis = {
    totalCalories: 670,
    breakdown: [
      { item: 'Grilled Chicken Breast', calories: 200, protein: 40, fat: 5, carbs: 0 },
      { item: 'Steamed Asparagus', calories: 50, protein: 5, fat: 0, carbs: 10 },
      { item: 'Sweet Potato Fries', calories: 420, protein: 4, fat: 20, carbs: 55 },
    ],
    totalMacros: {
      protein: 49,
      fat: 25,
      carbs: 65,
    }
  };

  return mockAnalysis;
};

// --- SCREENS ---

const Stack = createStackNavigator();
const windowWidth = Dimensions.get('window').width;

// Custom Header Component
const CustomHeader = ({ title }) => (
  <View style={{ flexDirection: 'row', alignItems: 'center', padding: 15, backgroundColor: DEEP_BLACK }}>
    <Image source={require('./assets/prepd_logo.png')} style={styles.banner} />
    <Text style={styles.headerTitle}>{title}</Text>
  </View>
);

const LaunchScreen = ({ navigation }) => {
  useEffect(() => {
    // Check permissions on launch (good practice)
    (async () => {
      const { status } = await ImagePicker.requestMediaLibraryPermissionsAsync();
      if (status !== 'granted') {
        alert('Prep\'d AI needs gallery permissions to select photos!');
      }
      await ImagePicker.requestCameraPermissionsAsync();
    })();

    setTimeout(() => navigation.replace('Login'), 2500);
  }, []);

  return (
    <LinearGradient colors={['#0a0a0a', '#021a1f']} style={styles.container}>
      <Image source={require('./assets/prepd_logo.png')} style={styles.logoLarge} />
      <Text style={styles.slogan}>Fuel Your Ambition</Text>
      <StatusBar style="light" />
    </LinearGradient>
  );
};

const LoginScreen = ({ navigation }) => {
  return (
    <SafeAreaView style={styles.container}>
      <StatusBar style="light" />
      <Image source={require('./assets/prepd_logo.png')} style={styles.logoSmall} />
      <Text style={styles.title}>Welcome to Prep'd AI</Text>
      <TouchableOpacity
        style={styles.button}
        onPress={() => navigation.navigate('Home')}
      >
        <Text style={styles.buttonText}>Continue (Simulated)</Text>
      </TouchableOpacity>
      <Text style={[styles.detailText, { marginTop: 20 }]}>
        *Firebase Auth Placeholder*
      </Text>
    </SafeAreaView>
  );
};

const HomeScreen = () => {
  const navigation = useNavigation();

  // Integrated camera functionality directly into the floating button logic
  const handleAIScan = async () => {
    Alert.alert(
      "AI Scan Method",
      "How would you like to log your meal?",
      [
        {
          text: "Take Photo",
          onPress: async () => {
            const result = await ImagePicker.launchCameraAsync({
              mediaTypes: ImagePicker.MediaTypeOptions.Images,
              allowsEditing: true,
              aspect: [4, 3],
              quality: 0.8,
            });
            if (!result.canceled && result.assets && result.assets.length > 0) {
              navigation.navigate('Analysis', { imageUri: result.assets[0].uri });
            }
          }
        },
        {
          text: "Choose from Gallery",
          onPress: async () => {
            const result = await ImagePicker.launchImageLibraryAsync({
              mediaTypes: ImagePicker.MediaTypeOptions.Images,
              allowsEditing: true,
              aspect: [4, 3],
              quality: 0.8,
            });
            if (!result.canceled && result.assets && result.assets.length > 0) {
              navigation.navigate('Analysis', { imageUri: result.assets[0].uri });
            }
          }
        },
        {
          text: "Cancel",
          style: "cancel"
        }
      ]
    );
  };

  return (
    <View style={styles.container}>
      <StatusBar style="light" />
      <Text style={styles.title}>Track Your Meals with AI</Text>
      <MaterialCommunityIcons name="food-fork-drink" size={windowWidth * 0.4} color={LIGHT_GRAY} style={{ opacity: 0.1 }} />

      {/* Floating Futuristic AI Scan Button */}
      <TouchableOpacity
        style={styles.floatingButton}
        onPress={handleAIScan}
      >
        <LinearGradient
          colors={['rgba(57, 255, 20, 0.5)', 'rgba(0, 255, 255, 0.5)']}
          style={{ position: 'absolute', width: 120, height: 120, borderRadius: 60, opacity: 0.4, blurRadius: 10 }}
        />
        <MaterialCommunityIcons name="scan-helper" size={50} color={NEON_GREEN} style={{ textShadowColor: NEON_GREEN, textShadowRadius: 5 }} />
        <Text style={styles.floatingButtonText}>AI Scan</Text>
      </TouchableOpacity>
    </View>
  );
};

// Analysis Screen with AI Simulation and Firebase Placeholder
const AnalysisScreen = ({ route }) => {
  const { imageUri } = route.params;
  const [loading, setLoading] = useState(true);
  const [analysisResult, setAnalysisResult] = useState(null);

  useEffect(() => {
    const runAnalysis = async () => {
      try {
        // 1. Simulate Firebase Upload
        const storageUrl = await simulateFirebaseUpload(imageUri);

        // 2. Simulate AI Vision Call
        const aiResult = await simulateAIVision(imageUri);

        setAnalysisResult({ ...aiResult, storageUrl });

        // 3. Simulate Firestore Save (Initial auto-save)
        await simulateFirebaseSave({
          timestamp: new Date().toISOString(),
          image: storageUrl,
          analysis: aiResult,
          status: 'Analyzed'
        });

      } catch (error) {
        Alert.alert("Analysis Error", "Failed to process the meal image.");
        console.error(error);
      } finally {
        setLoading(false);
      }
    };
    runAnalysis();
  }, [imageUri]);

  const renderAnalysisCard = () => {
    if (!analysisResult) return null;

    const { breakdown, totalCalories, totalMacros } = analysisResult;

    return (
      <ScrollView contentContainerStyle={{ alignItems: 'center', paddingVertical: 20 }}>
        <View style={styles.analysisCard}>
          <Text style={styles.analysisTitle}>AI Calorie Breakdown</Text>
          <Text style={[styles.detailText, { fontSize: 22, color: NEON_GREEN, marginBottom: 15 }]}>
            Total Estimated: {totalCalories} kcal
          </Text>

          <Text style={styles.macroTitle}>Detailed Items:</Text>
          {breakdown.map((item, index) => (
            <View key={index} style={{ marginBottom: 8, paddingLeft: 10, borderLeftWidth: 2, borderLeftColor: NEON_BLUE }}>
              <Text style={styles.detailText}>
                <Text style={{ fontWeight: 'bold' }}>{item.item}</Text> - {item.calories} kcal
              </Text>
              <Text style={[styles.detailText, { fontSize: 14, opacity: 0.7 }]}>
                P:{item.protein}g | F:{item.fat}g | C:{item.carbs}g
              </Text>
            </View>
          ))}

          <Text style={styles.macroTitle}>Total Macros:</Text>
          <View style={{ flexDirection: 'row', justifyContent: 'space-around', width: '100%', marginTop: 5 }}>
             <Text style={styles.detailText}><Text style={{ color: NEON_GREEN }}>P:</Text> {totalMacros.protein}g</Text>
             <Text style={styles.detailText}><Text style={{ color: NEON_GREEN }}>F:</Text> {totalMacros.fat}g</Text>
             <Text style={styles.detailText}><Text style={{ color: NEON_GREEN }}>C:</Text> {totalMacros.carbs}g</Text>
          </View>

          <Text style={[styles.detailText, { fontSize: 12, marginTop: 15, fontStyle: 'italic', opacity: 0.5 }]}>
            *Data is simulated and needs real Firebase/OpenAI keys to save/validate.
          </Text>
        </View>
      </ScrollView>
    );
  };

  return (
    <SafeAreaView style={styles.container}>
      <StatusBar style="light" />
      <Text style={[styles.title, { fontSize: 28, marginTop: 20 }]}>Meal Analysis</Text>

      {imageUri && (
        <Image source={{ uri: imageUri }} style={styles.imagePreview} />
      )}

      {loading ? (
        <View style={{ alignItems: 'center' }}>
          <ActivityIndicator size="large" color={NEON_GREEN} />
          <Text style={[styles.detailText, { marginTop: 15, color: NEON_GREEN }]}>
            Analyzing meal with Prep'd AI Vision...
          </Text>
        </View>
      ) : (
        <>
          {renderAnalysisCard()}
        </>
      )}
    </SafeAreaView>
  );
};

const DailyTrackerScreen = () => (
  <SafeAreaView style={styles.container}>
    <StatusBar style="light" />
    <Text style={styles.title}>Daily Tracker</Text>
    <Text style={styles.detailText}>Current: 0 / 2200 kcal</Text>
    <Text style={[styles.detailText, { opacity: 0.5, marginTop: 10 }]}>
      (Coming Soon: Goal setting and tracking dashboard)
    </Text>
  </SafeAreaView>
);

const HistoryScreen = () => (
  <SafeAreaView style={styles.container}>
    <StatusBar style="light" />
    <Text style={styles.title}>Meal History</Text>
    <Text style={[styles.detailText, { opacity: 0.5 }]}>
      (Coming Soon: List of all saved meals from Firestore)
    </Text>
  </SafeAreaView>
);

// --- MAIN APP ---

const AppStack = () => (
  <Stack.Navigator
    initialRouteName="Launch"
    screenOptions={{
      headerMode: 'screen',
      headerStyle: { backgroundColor: DEEP_BLACK },
      headerTintColor: NEON_GREEN,
      header: ({ route }) => {
        const titleMap = {
          Home: 'Prep\'d AI',
          Analysis: 'Analysis',
          DailyTracker: 'Daily Tracker',
          History: 'Meal History'
        };
        const title = titleMap[route.name] || route.name;
        if (route.name === 'Launch' || route.name === 'Login') return null;
        return <CustomHeader title={title} />;
      },
    }}
  >
    <Stack.Screen name="Launch" component={LaunchScreen} options={{ headerShown: false }} />
    <Stack.Screen name="Login" component={LoginScreen} options={{ headerShown: false }} />
    <Stack.Screen name="Home" component={HomeScreen} />
    <Stack.Screen name="Analysis" component={AnalysisScreen} />
    <Stack.Screen name="DailyTracker" component={DailyTrackerScreen} />
    <Stack.Screen name="History" component={HistoryScreen} />
  </Stack.Navigator>
);

export default function App() {
  return (
    <NavigationContainer theme={{ colors: { background: DARK_GRAY } }}>
      <AppStack />
    </NavigationContainer>
  );
}
