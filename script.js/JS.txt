// 1. Theme Toggle Functionality
const themeToggleBtn = document.getElementById('theme-toggle');
themeToggleBtn.addEventListener('click', () => {
  const currentTheme = document.documentElement.getAttribute('data-theme');
  const newTheme = currentTheme === 'light' ? 'dark' : 'light';
  document.documentElement.setAttribute('data-theme', newTheme);
  themeToggleBtn.textContent = newTheme === 'light' ? '🌙 Dark Mode' : '☀️ Light Mode';
});

// 2. LocalStorage Contact Form Handler
const contactForm = document.getElementById('contact-form');
const formStatus = document.getElementById('form-status');

contactForm.addEventListener('submit', (e) => {
  e.preventDefault();

  const name = document.getElementById('name').value;
  const email = document.getElementById('email').value;
  const message = document.getElementById('message').value;
  const timestamp = new Date().toLocaleString();

  const newResponse = { name, email, message, timestamp };

  // Store response in localStorage as JSON
  const existingResponses = JSON.parse(localStorage.getItem('userResponses')) || [];
  existingResponses.push(newResponse);
  localStorage.setItem('userResponses', JSON.stringify(existingResponses));

  formStatus.textContent = 'Response saved locally!';
  contactForm.reset();

  setTimeout(() => formStatus.textContent = '', 3000);

  // Auto-update admin panel if open
  if (!document.getElementById('admin-dashboard').classList.contains('hidden')) {
    renderAdminResponses();
  }
});

// 3. Admin Login & Dynamic Response Display
const adminLoginForm = document.getElementById('admin-login-form');
const adminDashboard = document.getElementById('admin-dashboard');
const loginError = document.getElementById('login-error');
const logoutBtn = document.getElementById('logout-btn');
const responsesContainer = document.getElementById('responses-container');

adminLoginForm.addEventListener('submit', (e) => {
  e.preventDefault();
  const password = document.getElementById('admin-pass').value;

  if (password === 'admin123') {
    adminLoginForm.classList.add('hidden');
    adminDashboard.classList.remove('hidden');
    loginError.textContent = '';
    renderAdminResponses();
  } else {
    loginError.textContent = 'Invalid Password! Use "admin123"';
  }
});

logoutBtn.addEventListener('click', () => {
  adminDashboard.classList.add('hidden');
  adminLoginForm.classList.remove('hidden');
  document.getElementById('admin-pass').value = '';
});

function renderAdminResponses() {
  const responses = JSON.parse(localStorage.getItem('userResponses')) || [];
  responsesContainer.innerHTML = '';

  if (responses.length === 0) {
    responsesContainer.innerHTML = '<p>No responses found.</p>';
    return;
  }

  responses.forEach((res) => {
    const item = document.createElement('div');
    item.className = 'response-card';
    item.innerHTML = `
      <p><strong>Name:</strong> ${escapeHtml(res.name)}</p>
      <p><strong>Email:</strong> ${escapeHtml(res.email)}</p>
      <p><strong>Message:</strong> ${escapeHtml(res.message)}</p>
      <p class="timestamp"><strong>Submitted at:</strong> ${res.timestamp}</p>
    `;
    responsesContainer.appendChild(item);
  });
}

function escapeHtml(str) {
  return str.replace(/[&<>"']/g, (m) => ({ '&': '&amp;', '<': '&lt;', '>': '&gt;', '"': '&quot;', "'": '&#039;' }[m]));
}