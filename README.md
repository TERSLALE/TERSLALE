mport React, { useState, useEffect } from 'react';
import { SafeAreaView, TextInput, Button, FlatList, Text, View } from 'react-native';
import io from 'socket.io-client';

const socket = io('http://localhost:3000');

const App = () => {
  const [username, setUsername] = useState('');
  const [message, setMessage] = useState('');
  const [messages, setMessages] = useState([]);
  const [registered, setRegistered] = useState(false);

  useEffect(() => {
    socket.on('newMessage', (msg) => {
      setMessages((prevMessages) => [...prevMessages, msg]);
    });
  }, []);

  const registerUser = () => {
    socket.emit('register', username);
    setRegistered(true);
  };

  const sendMessage = () => {
    socket.emit('sendMessage', message);
    setMessage('');
  };

  return (
    <SafeAreaView style={{ flex: 1, padding: 10 }}>
      {!registered ? (
        <View>
          <TextInput
            placeholder="Enter username"
            value={username}
            onChangeText={setUsername}
            style={{ borderWidth: 1, marginBottom: 10, padding: 5 }}
          />
          <Button title="Register" onPress={registerUser} />
        </View>
      ) : (
        <View style={{ flex: 1 }}>
          <FlatList
            data={messages}
            keyExtractor={(item, index) => index.toString()}
            renderItem={({ item }) => (
              <Text>
                {item.user}: {item.message}
              </Text>
            )}
            style={{ marginBottom: 10 }}
          />
          <TextInput
            placeholder="Enter message"
            value={message}
            onChangeText={setMessage}
            style={{ borderWidth: 1, marginBottom: 10, padding: 5 }}
          />
          <Button title="Send" onPress={sendMessage} />
        </View>
      )}
    </SafeAreaView>
  );
};

export default App;
