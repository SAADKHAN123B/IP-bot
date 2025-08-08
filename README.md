<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<title>Telegram Registration</title>
<style>
  body {
    background-color: #222;
    color: #ff4c4c;
    font-family: Arial, sans-serif;
    max-width: 400px;
    margin: 50px auto;
    padding: 20px;
    border-radius: 10px;
    box-shadow: 0 0 15px #ff4c4caa;
  }
  label, h1, h2 {
    color: #fff;
  }
  input, button {
    width: 100%;
    padding: 10px;
    margin: 8px 0 20px 0;
    border-radius: 5px;
    border: none;
    font-size: 1em;
  }
  #ip {
    background: black;
    color: #0f0;
    font-weight: bold;
    font-size: 1.5em;
    text-align: center;
    user-select: all;
    border-radius: 8px;
    padding: 15px;
  }
  button {
    background-color: #ff4c4c;
    color: white;
    font-weight: bold;
    cursor: pointer;
  }
  button:hover {
    background-color: #d43c3c;
  }
  #msg {
    text-align: center;
    font-weight: bold;
  }
</style>
</head>
<body>

<h1>Telegram Registration</h1>

<form id="regForm">
  <label for="username">Telegram Username (without @):</label>
  <input type="text" id="username" name="username" required placeholder="e.g. johndoe" />

  <label for="name">Telegram Name:</label>
  <input type="text" id="name" name="name" required placeholder="e.g. John Doe" />

  <h2>Your Public IP:</h2>
  <div id="ip">Loading...</div>

  <button type="submit">Register</button>
</form>

<div id="msg"></div>

<script>
  const ipDiv = document.getElementById('ip');
  const form = document.getElementById('regForm');
  const msgDiv = document.getElementById('msg');
  let currentIP = '';

  async function fetchIP() {
    try {
      const response = await fetch('https://api.ipify.org?format=json');
      const data = await response.json();
      currentIP = data.ip;
      ipDiv.textContent = currentIP;
    } catch {
      ipDiv.textContent = 'Unable to fetch IP';
    }
  }

  form.addEventListener('submit', async (e) => {
    e.preventDefault();
    const username = form.username.value.trim();
    const name = form.name.value.trim();

    if (!username || !name) {
      msgDiv.style.color = 'red';
      msgDiv.textContent = 'Please fill in all fields.';
      return;
    }
    if (!currentIP) {
      msgDiv.style.color = 'red';
      msgDiv.textContent = 'Could not detect your IP. Please reload and try again.';
      return;
    }

    // Send data to backend
    try {
      const res = await fetch('/register', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ username, name, ip: currentIP })
      });
      if (res.ok) {
        msgDiv.style.color = 'green';
        msgDiv.textContent = '✅ Registration successful! Admin has been notified.';
        form.reset();
      } else {
        throw new Error('Server error');
      }
    } catch (error) {
      msgDiv.style.color = 'red';
      msgDiv.textContent = '❌ Registration failed. Try again later.';
    }
  });

  fetchIP();
</script>

</body>
</html>
