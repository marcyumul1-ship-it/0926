// ===== MODULE 1: AUTHENTICATION ELEMENTS =====
const usernameInput = document.getElementById("username");
const passwordInput = document.getElementById("password");
const loginBtn = document.getElementById("loginBtn");
const logoutBtn = document.getElementById("logoutBtn");
const statusDisplay = document.getElementById("statusDisplay");
const auditDisplay = document.getElementById("auditDisplay");

// ===== MODULE 2 & 3: ORDER SELECTION & MODIFIERS =====
const drinkSelect = document.getElementById("drink-select");
const tempSelect = document.getElementById("temperature");
const sizeSelect = document.getElementById("size");
const milkSelect = document.getElementById("milk");
const addonSelect = document.getElementById("addon");

const addItemBtn = document.getElementById("addItemBtn");
const clearSelectionBtn = document.getElementById("clearSelectionBtn");

// ===== PAYMENT & CASH CALCULATION ELEMENTS =====
const cashInput = document.getElementById("cash");
const calcTotalBtn = document.getElementById("calcTotalBtn");
const checkoutBtn = document.getElementById("checkoutBtn");
const cancelOrderBtn = document.getElementById("cancelOrderBtn");

// ===== SYSTEM OUTPUT DISPLAY ELEMENTS =====
const terminalItems = document.getElementById("terminalItems");
const totalDueTxt = document.getElementById("totalDueTxt");
const cashTxt = document.getElementById("cashTxt");
const changeTxt = document.getElementById("changeTxt");
const receiptPre = document.getElementById("receiptPre");
const baristaPre = document.getElementById("baristaPre");
const printReceiptBtn = document.getElementById("printReceiptBtn");

// Global State
let activeTotal = 0;
let isAuthenticated = false;

// ===== MODULE 1: AUTHENTICATION LISTENERS =====
loginBtn.addEventListener("click", () => {
  const user = usernameInput.value.trim();
  const pass = passwordInput.value.trim();
  const timestamp = new Date().toLocaleTimeString();

  if (user !== "" && pass !== "") {
    isAuthenticated = true;
    statusDisplay.innerText = `Authenticated User: ${user}`;
    auditDisplay.innerText = `[${timestamp}] EVENT: LOGIN_SUCCESS | USER: ${user}`;
  } else {
    alert("Please enter a valid Username and Password.");
  }
});

logoutBtn.addEventListener("click", () => {
  isAuthenticated = false;
  usernameInput.value = "";
  passwordInput.value = "";
  statusDisplay.innerText = "[Not Authenticated]";
  auditDisplay.innerText = "[No Login Event Recorded]";
});

// ===== MODULE 2 & 3: CALCULATIONS & SELECTION LISTENERS =====
function computeCurrentTotal() {
  const drinkPrice = parseFloat(drinkSelect.value) || 0;
  const sizePrice = parseFloat(sizeSelect.value) || 0;
  const milkPrice = parseFloat(milkSelect.value) || 0;
  const addonPrice = parseFloat(addonSelect.value) || 0;

  activeTotal = drinkPrice + sizePrice + milkPrice + addonPrice;
  return activeTotal;
}

addItemBtn.addEventListener("click", () => {
  const total = computeCurrentTotal();
  
  if (drinkSelect.value === "" || drinkSelect.value === "0") {
    alert("Please select a drink base first.");
    return;
  }

  const selectedDrinkName = drinkSelect.options[drinkSelect.selectedIndex].text;
  terminalItems.innerText = `${selectedDrinkName} - Subtotal: ₱${total.toFixed(2)}`;
  totalDueTxt.innerText = `₱${total.toFixed(2)}`;
});

clearSelectionBtn.addEventListener("click", () => {
  drinkSelect.selectedIndex = 0;
  tempSelect.selectedIndex = 0;
  sizeSelect.selectedIndex = 0;
  milkSelect.selectedIndex = 0;
  addonSelect.selectedIndex = 0;
});

// ===== PAYMENT & CHECKOUT LISTENERS =====
calcTotalBtn.addEventListener("click", () => {
  const total = computeCurrentTotal();
  totalDueTxt.innerText = `₱${total.toFixed(2)}`;
});

checkoutBtn.addEventListener("click", () => {
  if (!isAuthenticated) {
    alert("Access Denied: Please log in first!");
    return;
  }

  const total = computeCurrentTotal();
  const cashTendered = parseFloat(cashInput.value) || 0;

  if (total === 0) {
    alert("Please add items to your order first.");
    return;
  }

  if (cashTendered < total) {
    alert("Insufficient cash provided!");
    return;
  }

  const changeDue = cashTendered - total;

  // Render monetary values on Terminal Screen
  cashTxt.innerText = `₱${cashTendered.toFixed(2)}`;
  changeTxt.innerText = `₱${changeDue.toFixed(2)}`;

  // Construct Receipt Text Output
  const drinkText = drinkSelect.options[drinkSelect.selectedIndex].text;
  const tempText = tempSelect.value || "N/A";
  const sizeText = sizeSelect.options[sizeSelect.selectedIndex].text;
  const milkText = milkSelect.options[milkSelect.selectedIndex].text;
  const addonText = addonSelect.options[addonSelect.selectedIndex].text;

  let receiptFormatted = "===================================\n";
  receiptFormatted += "           CAFE ELEVATE            \n";
  receiptFormatted += "===================================\n";
  receiptFormatted += `Item: ${drinkText}\n`;
  receiptFormatted += `Temp: ${tempText} | Size: ${sizeText}\n`;
  receiptFormatted += `Milk: ${milkText}\n`;
  receiptFormatted += `Add-on: ${addonText}\n`;
  receiptFormatted += "-----------------------------------\n";
  receiptFormatted += `TOTAL DUE:                    ₱${total.toFixed(2)}\n`;
  receiptFormatted += `CASH:                         ₱${cashTendered.toFixed(2)}\n`;
  receiptFormatted += `CHANGE:                       ₱${changeDue.toFixed(2)}\n`;
  receiptFormatted += "===================================";

  receiptPre.innerText = receiptFormatted;

  // Construct Barista Kitchen Display Output (JSON)
  const baristaJSON = {
    order_id: Math.floor(1000 + Math.random() * 9000),
    base_drink: drinkText,
    temperature: tempText,
    size: sizeText,
    customizations: [milkText, addonText]
  };

  baristaPre.innerText = JSON.stringify(baristaJSON, null, 2);
});

cancelOrderBtn.addEventListener("click", () => {
  drinkSelect.selectedIndex = 0;
  tempSelect.selectedIndex = 0;
  sizeSelect.selectedIndex = 0;
  milkSelect.selectedIndex = 0;
  addonSelect.selectedIndex = 0;

  activeTotal = 0;
  cashInput.value = "";
  terminalItems.innerText = "[No Items Selected]";
  totalDueTxt.innerText = "₱0.00";
  cashTxt.innerText = "₱0.00";
  changeTxt.innerText = "₱0.00";
  receiptPre.innerText = "";
  baristaPre.innerText = "";
});

printReceiptBtn.addEventListener("click", () => {
  if (!receiptPre.innerText.trim()) {
    alert("No active transaction ready for printing.");
    return;
  }
  window.print();
});
    <br>
    <p><button type="button">Print Receipt</button></p>
    <!-- ACTION: Triggers hardware printer command -->
