import { StatusBar } from 'expo-status-bar';
import { useEffect, useState } from 'react';
import { ActivityIndicator, ScrollView, StyleSheet, Text, View } from 'react-native';

export default function App() {
  const [data, setData] = useState([]);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState(null);

  const API_KEY = "AIzaSyD36C-k9xtxWnkzTv5RxZIf-rqyAtLWed4";
  const SPREADSHEET_ID = "1wDbk4BzF7JhCUHV6t3mcOySLpdWnpR9jTSWcyTSraL0";
  const RANGE = "A1:B10"; 
  const url = `https://sheets.googleapis.com/v4/spreadsheets/${SPREADSHEET_ID}/values/${RANGE}?key=${API_KEY}`;

  useEffect(() => {
    const loadPrice = async () => {
      try {
        const response = await fetch(url);

        if (!response.ok) {
          throw new Error(`Erro HTTP! Código: ${response.status}`);
        }

        const dataPrice = await response.json();
        setData(dataPrice.values || []); // Pegamos apenas os valores da planilha
      } catch (error) {
        setError(error.message);
      } finally {
        setIsLoading(false);
      }
    };

    loadPrice();
  }, []);

  return (
    <View style={styles.container}>
      <StatusBar style="auto" />
      {isLoading ? (
        <ActivityIndicator size="large" color="#0000ff" />
      ) : error ? (
        <Text style={{ color: 'red' }}>Erro: {error}</Text>
      ) : (
        <ScrollView>
          {data.map((row, index) => (
            <View key={index} style={styles.row}>
              {row.map((cell, cellIndex) => (
                <Text key={cellIndex} style={styles.cell}>{cell}</Text>
              ))}
            </View>
          ))}
        </ScrollView>
      )}
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    padding: 20,
  },
  row: {
    flexDirection: 'row',
    marginBottom: 10,
  },
  cell: {
    flex: 1,
    borderWidth: 1,
    padding: 8,
    textAlign: 'center',
  },
});
