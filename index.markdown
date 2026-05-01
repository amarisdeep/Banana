---
title: "Home"
permalink: /
layout: home
icon: "/Banana/assets/img/logo.jpg"
---

<style>
  .home-container {
    padding: 140px 20px 60px;
    max-width: 1200px;
    margin: 0 auto;
  }

  .section-title {
    font-size: 1.2rem;
    font-weight: 700;
    letter-spacing: 4px;
    text-transform: uppercase;
    margin-bottom: 50px;
    color: var(--accent-blue, #00d4ff);
    text-align: center;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 30px;
  }

  .section-title::before, .section-title::after {
    content: "";
    height: 1px;
    width: 60px;
    background: rgba(255,255,255,0.1);
  }

  /* --- Art Grid --- */
  .art-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(320px, 1fr));
    gap: 40px;
    margin-bottom: 100px;
  }

  .art-card {
    background: rgba(255, 255, 255, 0.02);
    border: 1px solid rgba(255, 255, 255, 0.05);
    border-radius: 24px;
    padding: 20px;
    text-decoration: none;
    transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
    display: block;
    text-align: center;
  }

  .art-card:hover {
    transform: translateY(-10px);
    background: rgba(255, 255, 255, 0.04);
    border-color: var(--accent-blue, #00d4ff);
    box-shadow: 0 30px 60px rgba(0,0,0,0.5);
  }

  .card-img-wrapper {
    width: 100%;
    aspect-ratio: 1 / 1;
    border-radius: 16px;
    overflow: hidden;
    margin-bottom: 20px;
    background: #111;
  }

  .card-img-wrapper img {
    width: 100%;
    height: 100%;
    object-fit: cover;
    transition: 0.6s ease;
  }

  .art-card:hover img { transform: scale(1.1); }

  .card-title {
    font-size: 1.4rem;
    font-weight: 700;
    color: #fff;
    margin-bottom: 8px;
  }

  .card-price {
    font-size: 1.1rem;
    color: var(--accent-blue, #00d4ff);
    font-weight: 600;
  }

  .sold-badge {
    font-size: 10px;
    background: #ff4d4d;
    color: #fff;
    padding: 3px 10px;
    border-radius: 4px;
    margin-left: 10px;
    letter-spacing: 1px;
  }

  /* --- Commission Card Styles --- */
  .commission-section {
    margin-top: 60px;
    padding: 60px 40px;
    background: linear-gradient(145deg, rgba(255,255,255,0.03), rgba(0,212,255,0.02));
    border: 1px solid rgba(255, 255, 255, 0.05);
    border-radius: 32px;
    text-align: center;
    transition: 0.4s;
    text-decoration: none;
    display: block;
  }

  .commission-section:hover {
    border-color: var(--accent-blue, #00d4ff);
    background: rgba(255, 255, 255, 0.05);
    transform: translateY(-5px);
    box-shadow: 0 20px 40px rgba(0,0,0,0.3);
  }

  .com-tag {
    font-family: monospace;
    font-size: 12px;
    letter-spacing: 4px;
    color: var(--accent-blue, #00d4ff);
    margin-bottom: 15px;
    display: block;
  }

  .com-title {
    font-size: 2.5rem;
    font-weight: 800;
    color: #fff;
    margin: 0 0 15px;
    letter-spacing: -1px;
  }

  .com-text {
    color: #888;
    max-width: 500px;
    margin: 0 auto 30px;
    line-height: 1.6;
  }

  .com-btn {
    display: inline-block;
    padding: 15px 40px;
    background: #fff;
    color: #000;
    border-radius: 100px;
    font-weight: 700;
    font-size: 12px;
    text-transform: uppercase;
    letter-spacing: 1px;
    transition: 0.3s;
  }

  .commission-section:hover .com-btn {
    background: var(--accent-blue, #00d4ff);
    transform: scale(1.05);
  }

  @media (max-width: 768px) {
    .com-title { font-size: 1.8rem; }
  }
</style>

<div class="home-container">

  <h2 class="section-title">Latest Creations</h2>
  <div class="art-grid">
    {% assign latest_art = site.data.Art | reverse %}
    {% for item in latest_art limit:3 %}
      {% assign url_title = item.title | slugify %}
      {% assign first_image = item.image_url | split: ',' | first | strip %}
      
      <a href="/Banana/artwork/?title={{ url_title }}" class="art-card">
        <div class="card-img-wrapper">
          <img src="{{ first_image }}" alt="{{ item.title }}">
        </div>
        <h3 class="card-title">{{ item.title }}</h3>
        <p class="card-price">
            ₹{{ item.price }}
            {% if item.stock == "0" %}<span class="sold-badge">SOLD OUT</span>{% endif %}
        </p>
      </a>
    {% endfor %}
  </div>

  <h2 class="section-title">Curated Selection</h2>
  <div class="art-grid">
    {% for item in site.data.Art limit:3 offset:1 %}
      {% assign url_title = item.title | slugify %}
      {% assign first_image = item.image_url | split: ',' | first | strip %}
      
      <a href="/Banana/artwork/?title={{ url_title }}" class="art-card">
        <div class="card-img-wrapper">
          <img src="{{ first_image }}" alt="{{ item.title }}">
        </div>
        <h3 class="card-title">{{ item.title }}</h3>
        <p class="card-price">₹{{ item.price }}</p>
      </a>
    {% endfor %}
  </div>

  <!-- COMMISSION SECTION -->
  <a href="https://forms.gle/TjrEEznsM32EVfxt5" target="_blank" class="commission-section">
    <span class="com-tag">CUSTOM PROJECT</span>
    <h2 class="com-title">Request a Commission</h2>
    <p class="com-text">Have a specific vision in mind? I'm currently accepting custom requests for unique, personalized artworks.</p>
    <span class="com-btn">Open Request Form →</span>
  </a>

</div>
