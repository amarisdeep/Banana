---
layout: home
permalink: /collection/
---

<style>
  /* --- Layout & Spacing --- */
  .collection-wrapper {
    max-width: 1000px;
    margin: 0 auto;
    /* Added 140px padding to clear the fixed header */
    padding: 0px 20px 60px; 
  }

  .collection-title-section {
    margin-bottom: 50px;
    text-align: left;
  }

  .collection-title-section h1 {
    font-size: 2.5rem;
    font-weight: 700;
    margin: 0;
    color: #fff;
    text-transform: capitalize;
  }

  .collection-title-section p {
    color: var(--accent-blue);
    font-family: monospace;
    font-size: 0.9rem;
    margin-top: 5px;
  }

  /* --- Simple Horizontal Card --- */
  .art-list {
    display: flex;
    flex-direction: column;
    gap: 40px;
  }

  .art-card-row {
    display: grid;
    grid-template-columns: 320px 1fr;
    gap: 30px;
    background: rgba(255, 255, 255, 0.03);
    border: 1px solid rgba(255, 255, 255, 0.08);
    border-radius: 16px;
    overflow: hidden;
    padding: 20px;
    transition: 0.3s ease;
  }

  .art-card-row:hover {
    background: rgba(255, 255, 255, 0.05);
    border-color: var(--accent-blue);
  }

  .card-img-box {
    width: 100%;
    height: 240px;
    border-radius: 10px;
    overflow: hidden;
  }

  .card-img-box img {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }

  .card-content {
    display: flex;
    flex-direction: column;
    justify-content: center;
  }

  .card-content h2 {
    font-size: 1.8rem;
    margin: 0 0 10px;
    color: #fff;
  }

  .card-content p {
    color: #aaa;
    font-size: 1rem;
    line-height: 1.5;
    margin-bottom: 20px;
    max-width: 500px;
  }

  .card-footer {
    display: flex;
    align-items: center;
    gap: 25px;
  }

  .price {
    font-size: 1.4rem;
    font-weight: 700;
    color: var(--accent-blue);
  }

  /* --- Dynamic Button Style --- */
  .btn-action {
    padding: 10px 24px;
    border-radius: 8px;
    font-weight: 600;
    font-size: 0.85rem;
    text-transform: uppercase;
    cursor: pointer;
    transition: 0.3s;
    border: none;
    letter-spacing: 1px;
  }

  .btn-add { background: #fff; color: #000; }
  .btn-added { background: var(--accent-blue); color: #000; cursor: default; }
  .btn-add:hover { background: var(--accent-blue); }

  @media (max-width: 768px) {
    .art-card-row { grid-template-columns: 1fr; }
    .card-img-box { height: 300px; }
    .collection-title-section h1 { font-size: 2rem; }
  }
</style>

<div class="collection-wrapper">
    <div class="collection-title-section">
        <h1 id="page-title">Collection</h1>
        <p id="item-count">Loading collection...</p>
    </div>

    <div class="art-list" id="art-container"></div>
</div>

<script>
    const urlParams = new URLSearchParams(window.location.search);
    const selectedCol = urlParams.get('collection');
    
    const artData = [
        {% for item in site.data.Art %}
        {
            id: "{{ item.id }}",
            title: "{{ item.title }}",
            collection: "{{ item.collection | slugify }}",
            colName: "{{ item.collection }}",
            price: "{{ item.price }}",
            // FIXED: We use the first image from the array for the thumbnail
            images: "{{ item.image_url }}".split(',').map(s => s.trim()),
            desc: "{{ item.desc | escape }}"
        }{% unless forloop.last %},{% endunless %}
        {% endfor %}
    ];

    function renderCollection() {
        const container = document.getElementById('art-container');
        const cart = JSON.parse(localStorage.getItem('art_cart')) || [];
        
        if (!selectedCol) { window.location.href = "/"; return; }

        const filtered = artData.filter(a => a.collection === selectedCol);
        document.getElementById('page-title').innerText = filtered[0] ? filtered[0].colName : "Collection";
        document.getElementById('item-count').innerText = `${filtered.length} Artworks Available`;

        container.innerHTML = filtered.map(art => {
            const isAdded = cart.some(c => c.id === art.id);
            const btnClass = isAdded ? 'btn-added' : 'btn-add';
            const btnText = isAdded ? 'ADDED' : 'ADD TO CART';
            const artLink = `/artwork/?title=${art.title.replace(/\s+/g, '-').toLowerCase()}`;
            
            // FIXED: Accessing art.images[0] instead of art.img
            return `
                <div class="art-card-row">
                    <a href="${artLink}" class="card-img-box">
                        <img src="${art.images[0]}" alt="${art.title}">
                    </a>
                    <div class="card-content">
                        <h2>${art.title}</h2>
                        <p>${art.desc}</p>
                        <div class="card-footer">
                            <span class="price">₹${art.price}</span>
                            <button 
                                id="btn-${art.id}" 
                                class="btn-action ${btnClass}" 
                                onclick="toggleCart('${art.id}', this)">
                                ${btnText}
                            </button>
                        </div>
                    </div>
                </div>
            `;
        }).join('');
    }

    function toggleCart(id, btn) {
        let cart = JSON.parse(localStorage.getItem('art_cart')) || [];
        const isAdded = cart.some(c => c.id === id);

        if (!isAdded) {
            const item = artData.find(a => a.id === id);
            cart.push(item);
            localStorage.setItem('art_cart', JSON.stringify(cart));
            
            btn.innerText = "ADDED";
            btn.classList.remove('btn-add');
            btn.classList.add('btn-added');
            
            if (typeof updateCartCount === 'function') updateCartCount();
        }
    }

    document.addEventListener('DOMContentLoaded', renderCollection);
</script>
