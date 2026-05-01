---
layout: home
permalink: /Banana/artwork/
---

<style>
  /* --- Professional Stage Layout --- */
  .artwork-stage {
    padding: 140px 20px 80px;
    max-width: 1200px;
    margin: 0 auto;
    display: grid;
    grid-template-columns: 1.2fr 1fr; 
    gap: 60px;
    align-items: start;
  }

  .slider-container {
    position: sticky;
    top: 140px;
    height: calc(100vh - 200px);
    display: flex;
    align-items: center;
  }

  .slider-viewport {
    position: relative;
    background: #141414;
    border-radius: 24px;
    border: 1px solid rgba(255, 255, 255, 0.05);
    overflow: hidden;
    width: 100%;
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    box-shadow: 0 40px 100px rgba(0,0,0,0.6);
  }

  .slider-viewport img {
    max-width: 90%;
    max-height: 90%;
    object-fit: contain;
    transition: transform 0.4s cubic-bezier(0.165, 0.84, 0.44, 1);
    cursor: zoom-in;
  }

  .slider-viewport.is-zoomed img {
    transform: scale(2);
    cursor: zoom-out;
  }

  .nav-arrow {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    background: rgba(0, 0, 0, 0.6);
    backdrop-filter: blur(10px);
    color: white;
    border: 1px solid rgba(255,255,255,0.1);
    width: 45px;
    height: 45px;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    align-items: center;
    justify-content: center;
    z-index: 10;
  }
  .nav-arrow:hover { background: var(--accent-blue); color: #000; }
  .arrow-prev { left: 20px; }
  .arrow-next { right: 20px; }

  /* --- Sidebar Info --- */
  .art-info-sidebar {
    display: flex;
    flex-direction: column;
    gap: 30px;
    text-align: left;
    padding-bottom: 100px;
  }

  /* Clickable Collection Link Style */
  .col-link {
    text-decoration: none;
    display: inline-block;
    transition: 0.3s;
  }

  .col-label {
    font-family: monospace;
    font-size: 11px;
    color: var(--accent-blue);
    letter-spacing: 3px;
    text-transform: uppercase;
    cursor: pointer;
  }

  .col-link:hover .col-label {
    opacity: 0.7;
    letter-spacing: 4px; /* Subtle expansion effect */
  }

  .art-title {
    font-size: 3rem;
    font-weight: 800;
    line-height: 1.1;
    margin: 10px 0;
  }

  .art-desc {
    font-size: 1.1rem;
    color: #999;
    line-height: 1.8;
    border-bottom: 1px solid #222;
    padding-bottom: 40px;
  }

  .acquisition-box {
    padding: 30px;
    background: rgba(255,255,255,0.02);
    border-radius: 20px;
    border: 1px solid rgba(255,255,255,0.05);
  }

  .price-display { font-size: 2.2rem; font-weight: 800; margin-bottom: 5px; }

  .stock-status {
    display: flex;
    align-items: center;
    gap: 8px;
    font-size: 11px;
    font-family: monospace;
    color: #666;
    margin-bottom: 30px;
  }

  .dot { width: 8px; height: 8px; border-radius: 50%; }

  .btn-action {
    width: 100%;
    padding: 20px;
    border-radius: 12px;
    font-weight: 700;
    text-transform: uppercase;
    cursor: pointer;
    transition: 0.3s;
    border: none;
  }

  .btn-buy { background: #fff; color: #000; }
  .btn-buy:hover { background: var(--accent-blue); transform: translateY(-3px); }
  .btn-added { background: var(--accent-blue) !important; color: #000 !important; }

  @media (max-width: 1000px) {
    .artwork-stage { grid-template-columns: 1fr; padding-top: 100px; }
    .slider-container { position: relative; top: 0; height: 50vh; }
  }
</style>

<div class="artwork-stage" id="main-content"></div>

<script>
  const urlParams = new URLSearchParams(window.location.search);
  const titleQuery = urlParams.get('title');
  
  const artData = [
    {% for item in site.data.Art %}
    {
      id: "{{ item.id }}",
      title: "{{ item.title }}",
      collection: "{{ item.collection }}",
      colSlug: "{{ item.collection | slugify }}",
      price: "{{ item.price }}",
      stock: {{ item.stock | default: 0 }},
      images: "{{ item.image_url }}".split(',').map(s => s.trim()),
      desc: "{{ item.desc | escape }}"
    }{% unless forloop.last %},{% endunless %}
    {% endfor %}
  ];

  let currentImgIndex = 0;

  function renderArtwork() {
    const art = artData.find(a => a.title.toLowerCase() === titleQuery.replace(/-/g, ' ').toLowerCase());
    if (!art) { window.location.href = "/"; return; }

    const cart = JSON.parse(localStorage.getItem('art_cart')) || [];
    const inCart = cart.some(c => c.id === art.id);
    const isAvailable = art.stock > 0;

    document.getElementById('main-content').innerHTML = `
      <div class="slider-container">
        <div class="slider-viewport" id="viewport">
          <button class="nav-arrow arrow-prev" id="prevBtn">←</button>
          <img src="${art.images[0]}" alt="${art.title}" id="mainImg" draggable="false">
          <button class="nav-arrow arrow-next" id="nextBtn">→</button>
        </div>
      </div>

      <aside class="art-info-sidebar">
        <div class="meta-section">
          <!-- Clickable Collection Tag -->
          <a href="/collection/?collection=${art.colSlug}" class="col-link">
            <span class="col-label">${art.collection}</span>
          </a>
          <h1 class="art-title">${art.title}</h1>
        </div>
        
        <p class="art-desc">${art.desc}</p>

        <div class="acquisition-box">
          <div class="price-display">₹${art.price}</div>
          <div class="stock-status">
            <span class="dot" style="background: ${isAvailable ? 'var(--accent-blue)' : '#ff4444'}"></span>
            ${isAvailable ? 'AVAILABLE FOR ACQUISITION' : 'OUT OF STOCK'}
          </div>
          
          <button id="cartBtn" class="btn-action ${inCart ? 'btn-added' : 'btn-buy'}">
            ${inCart ? 'VIEW IN CART →' : 'ADD TO COLLECTION'}
          </button>
        </div>
      </aside>
    `;

    // Initialize Slider/Cart/Zoom
    setupInteractiveFeatures(art);
  }

  function setupInteractiveFeatures(art) {
    const btn = document.getElementById('cartBtn');
    btn.onclick = () => handleCartAction(btn, art);

    if (art.images.length > 1) {
      document.getElementById('prevBtn').style.display = 'flex';
      document.getElementById('nextBtn').style.display = 'flex';
      document.getElementById('prevBtn').onclick = () => changeImg(-1, art.images);
      document.getElementById('nextBtn').onclick = () => changeImg(1, art.images);
    }

    const vp = document.getElementById('viewport');
    const img = document.getElementById('mainImg');
    vp.onclick = (e) => { if (e.target.tagName === 'IMG') vp.classList.toggle('is-zoomed'); };
    vp.onmousemove = (e) => {
      if (vp.classList.contains('is-zoomed')) {
        const rect = vp.getBoundingClientRect();
        img.style.transformOrigin = `${((e.clientX - rect.left) / rect.width) * 100}% ${((e.clientY - rect.top) / rect.height) * 100}%`;
      }
    };
  }

  function changeImg(step, images) {
    currentImgIndex = (currentImgIndex + step + images.length) % images.length;
    document.getElementById('mainImg').src = images[currentImgIndex];
  }

  function handleCartAction(btn, art) {
    if (btn.classList.contains('btn-added')) {
      window.location.href = '/cart/';
      return;
    }
    let cart = JSON.parse(localStorage.getItem('art_cart')) || [];
    if (!cart.some(c => c.id === art.id)) {
      cart.push(art);
      localStorage.setItem('art_cart', JSON.stringify(cart));
      btn.innerText = "VIEW IN CART →";
      btn.classList.add('btn-added');
      if (typeof updateCartCount === 'function') updateCartCount();
    }
  }

  if (titleQuery) renderArtwork();
</script>
