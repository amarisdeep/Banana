---
layout: home
title: "Your Cart"
permalink: /cart/
---

<style>
  /* --- Gallery Cart Polish --- */
  .cart-stage {
    padding: 0px 20px 100px; /* Clear fixed header */
    max-width: 850px;
    margin: 0 auto;
    color: #fff;
    text-align: center;
  }

  .cart-header-title {
    font-size: 3.5rem;
    font-weight: 800;
    letter-spacing: -3px;
    margin-bottom: 50px;
    background: linear-gradient(to bottom, #fff, #444);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
  }

  /* --- Item Cards --- */
  .cart-item {
    display: flex;
    align-items: center;
    gap: 25px;
    background: rgba(255,255,255,0.03);
    backdrop-filter: blur(10px);
    border: 1px solid rgba(255,255,255,0.08);
    padding: 25px;
    border-radius: 28px;
    margin-bottom: 20px;
  }

  .cart-item img { 
    width: 100px; 
    height: 110px; /* Increased height by 10% (from 100px) */
    object-fit: cover; 
    border-radius: 16px;
    background: #111;
  }

  .cart-info { flex-grow: 1; text-align: left; }
  .cart-info h3 { margin: 0; font-size: 1.4rem; font-weight: 700; }
  .cart-info p { margin: 5px 0; color: #888; font-family: monospace; font-size: 0.9rem; }

  /* --- Controls --- */
  .item-actions {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 12px;
  }

  .qty-controls {
    display: flex;
    align-items: center;
    gap: 15px;
    background: rgba(0,0,0,0.3);
    padding: 8px 15px;
    border-radius: 100px;
    border: 1px solid rgba(255,255,255,0.1);
  }

  .qty-btn {
    background: none; border: none; color: #fff;
    font-size: 18px; font-weight: bold; cursor: pointer;
    width: 25px; transition: 0.2s;
  }
  .qty-btn:hover { color: var(--accent-blue, #00d4ff); }
  
  .qty-val { font-family: monospace; font-weight: 700; width: 20px; text-align: center; }

  .remove-link {
    color: #ff4d4d; background: none; border: none;
    cursor: pointer; font-size: 9px; text-transform: uppercase;
    letter-spacing: 1.5px; opacity: 0.5; transition: 0.3s;
  }
  .remove-link:hover { opacity: 1; }

  /* --- Footer & Summary --- */
  .cart-footer {
    margin-top: 50px;
    padding: 45px;
    background: rgba(255, 255, 255, 0.02);
    border: 1px solid rgba(255,255,255,0.05);
    border-radius: 40px;
  }

  .total-price { font-size: 3.5rem; font-weight: 800; margin-bottom: 35px; }

  .checkout-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 15px; }

  .action-link {
    display: flex; align-items: center; justify-content: center;
    padding: 20px; text-decoration: none; font-weight: 700;
    font-size: 11px; text-transform: uppercase; border-radius: 18px;
    transition: 0.3s;
  }

  .btn-wa { background: #25D366; color: #fff; }
  .btn-ig { background: #fff; color: #000; }
  .btn-mail { border: 1px solid rgba(255,255,255,0.1); color: #fff; }

  /* --- Empty State Button --- */
  .btn-home {
    display: inline-block;
    margin-top: 20px;
    padding: 15px 30px;
    background: var(--accent-blue, #00d4ff);
    color: #000;
    text-decoration: none;
    border-radius: 12px;
    font-weight: 700;
    text-transform: uppercase;
  }

  @media (max-width: 768px) {
    .cart-item { flex-direction: column; text-align: center; }
    .checkout-grid { grid-template-columns: 1fr; }
  }
</style>

<div class="cart-stage">
    
    <div id="cart-list"></div>
    
    <div id="cart-empty" style="display:none; padding: 60px 0;">
        <p style="color: #666; font-family: monospace; margin-bottom: 20px;">// NO_ITEMS_IN_CART_SYSTEM_IDLE</p>
        <a href="/" class="btn-home">Go To Home Page</a>
    </div>

    <div id="cart-summary" class="cart-footer">
        <div class="total-price" id="total-amount">₹0</div>
        <div class="checkout-grid">
            <a id="check-wa" href="#" target="_blank" class="action-link btn-wa">WhatsApp</a>
            <a id="check-ig" href="#" target="_blank" class="action-link btn-ig">Instagram</a>
            <a id="check-mail" href="#" class="action-link btn-mail">Email</a>
        </div>
    </div>
</div>

<script>
  function loadCart() {
    const cart = JSON.parse(localStorage.getItem('art_cart')) || [];
    const list = document.getElementById('cart-list');
    const summary = document.getElementById('cart-summary');
    const empty = document.getElementById('cart-empty');
    
    if (cart.length === 0) {
      list.innerHTML = '';
      summary.style.display = 'none';
      empty.style.display = 'block';
      if (typeof updateCartCount === 'function') updateCartCount();
      return;
    }

    empty.style.display = 'none';
    summary.style.display = 'block';
    
    let total = 0;
    let messageBody = "ORDER INQUIRY:\n\n";

    list.innerHTML = cart.map((item, index) => {
      const qty = item.qty || 1;
      const itemSubtotal = parseInt(item.price) * qty;
      total += itemSubtotal;
      
      // FIX: Handle comma-separated images and clean paths
      let displayImage = "";
      if (item.images && Array.isArray(item.images)) {
          displayImage = item.images[0];
      } else if (item.image_url) {
          displayImage = item.image_url.split(',')[0].trim();
      }

      messageBody += `${item.id} ${item.title} ₹${item.price} [${qty}pcs]\n`;

      return `
        <div class="cart-item">
            <img src="${displayImage}" alt="${item.title}" onerror="this.src='/assets/img/logo.png'">
            <div class="cart-info">
                <h3>${item.title}</h3>
                <p>₹${item.price} // REF: ${item.id}</p>
            </div>
            <div class="item-actions">
                <div class="qty-controls">
                    <button class="qty-btn" onclick="updateQty(${index}, -1)">-</button>
                    <span class="qty-val">${qty}</span>
                    <button class="qty-btn" onclick="updateQty(${index}, 1)">+</button>
                </div>
                <button class="remove-link" onclick="removeItem(${index})">Remove Piece</button>
            </div>
        </div>
      `;
    }).join('');

    document.getElementById('total-amount').innerText = `₹${total}`;
    
    const encodedMsg = encodeURIComponent(messageBody + `\nTOTAL: ₹${total}`);
    document.getElementById('check-wa').href = `https://wa.me/amarisdeep?text=${encodedMsg}`;
    document.getElementById('check-mail').href = `mailto:amarisdeep@gmail.com?subject=Order&body=${encodedMsg}`;
    
    if (typeof updateCartCount === 'function') updateCartCount();
  }

  function updateQty(index, change) {
    let cart = JSON.parse(localStorage.getItem('art_cart')) || [];
    cart[index].qty = Math.max(1, (cart[index].qty || 1) + change);
    localStorage.setItem('art_cart', JSON.stringify(cart));
    loadCart();
  }

  function removeItem(index) {
    let cart = JSON.parse(localStorage.getItem('art_cart')) || [];
    cart.splice(index, 1);
    localStorage.setItem('art_cart', JSON.stringify(cart));
    loadCart();
  }

  window.onload = loadCart;
</script>
