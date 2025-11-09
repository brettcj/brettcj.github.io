---
permalink: /work/
title: ""
toc: false
---

<style>
/* Gallery Controls */
.gallery-controls {
  margin-bottom: 15px;
  display: flex;
  gap: 10px;
  flex-wrap: wrap;
}

.gallery-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
  gap: 10px;
}

.gallery-item {
  position: relative;
  width: 100%;
  aspect-ratio: 1/1;
  overflow: hidden;
  cursor: pointer;
}

.gallery-item img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: opacity 0.3s ease;
}

.gallery-item:hover img { opacity: 0.4; }

.gallery-text {
  position: absolute;
  top: 50%; left: 50%;
  transform: translate(-50%, -50%);
  font-size: 14px;
  font-weight: bold;
  color: white;
  opacity: 0;
  transition: opacity 0.3s ease;
}

.gallery-item:hover .gallery-text { opacity: 1; }

/* Lightbox Overlay */
#lightbox-modal {
  display: none;
  position: fixed;
  top: 0; left: 0;
  width: 100vw; height: 100vh;
  background: rgba(0,0,0,0.95);
  justify-content: center;
  align-items: center;
  flex-direction: column;
  padding: 20px;
  text-align: center;
  color: white;
  z-index: 9999;
}

#lightbox-img {
  max-width: 90%;
  max-height: 80vh;
  margin-bottom: 10px;
}

#lightbox-caption {
  font-size: 18px;
  margin-bottom: 5px;
}

#lightbox-doi a {
  color: #72baff;
  text-decoration: underline;
  font-size: 14px;
}

/* Navigation Buttons */
.lightbox-nav {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  font-size: 36px;
  color: white;
  background: rgba(0,0,0,0.5);
  padding: 10px;
  cursor: pointer;
  user-select: none;
}

#lightbox-prev { left: 10px; }
#lightbox-next { right: 10px; }

</style>

<!-- FILTER BUTTONS -->
<div class="gallery-controls">
  <button onclick="filterGallery('*')">All</button>
  {% assign all_tags = site.data.gallery | map: 'tags' | join: ',' | split: ',' | uniq %}
  {% for tag in all_tags %}
    <button onclick="filterGallery('{{ tag }}')">{{ tag }}</button>
  {% endfor %}
</div>

<!-- GALLERY -->
<div class="gallery-grid">
  {% for item in site.data.gallery %}
  <div class="gallery-item"
       data-tags="{{ item.tags | join: ' ' }}"
       data-index="{{ forloop.index0 }}"
       onclick="openLightbox({{ forloop.index0 }})">
    <img src="/assets/images/gallery/{{ item.file }}" alt="{{ item.caption }}">
    <div class="gallery-text">{{ item.caption }}</div>
  </div>
  {% endfor %}
</div>


<!-- Lightbox -->
<div id="lightbox-modal">
  <span id="lightbox-prev" class="lightbox-nav" onclick="prevImage(event)">&#10094;</span>
  <img id="lightbox-img" src="" draggable="false">
  <span id="lightbox-next" class="lightbox-nav" onclick="nextImage(event)">&#10095;</span>
  <div id="lightbox-caption"></div>
  <div id="lightbox-doi"></div>
</div>

<style>
#lightbox-img {
  max-width: 70%;
  max-height: 60vh;
  margin-bottom: 10px;
  -webkit-user-drag: none;  /* Safari */
  user-drag: none;          /* Chrome/Opera */
  pointer-events: auto;
}
</style>

<script>
let galleryData = [
  {% for item in site.data.gallery %}
  {
    src: '/assets/images/gallery/{{ item.file }}',
    caption: '{{ item.caption | escape }}',
    doi: '{{ item.doi }}'
  }{% if forloop.last == false %},{% endif %}
  {% endfor %}
];

let currentIndex = 0;

function filterGallery(tag) {
  document.querySelectorAll('.gallery-item').forEach(el => {
    el.style.display = (tag === '*' || el.dataset.tags.includes(tag)) ? 'block' : 'none';
  });
}

function openLightbox(index) {
  currentIndex = index;
  updateLightbox();
  document.getElementById('lightbox-modal').style.display = 'flex';
}

function updateLightbox() {
  let item = galleryData[currentIndex];
  document.getElementById('lightbox-img').src = item.src;
  document.getElementById('lightbox-caption').textContent = item.caption;
  document.getElementById('lightbox-doi').innerHTML = item.doi ? `<a href="${item.doi}" target="_blank">DOI: ${item.doi}</a>` : '';
}

function closeLightbox() {
  document.getElementById('lightbox-modal').style.display = 'none';
}

function prevImage(event) {
  event.stopPropagation();
  currentIndex = (currentIndex - 1 + galleryData.length) % galleryData.length;
  updateLightbox();
}

function nextImage(event) {
  event.stopPropagation();
  currentIndex = (currentIndex + 1) % galleryData.length;
  updateLightbox();
}

// Close when clicking outside image
document.getElementById('lightbox-modal').addEventListener('click', function(e) {
  if (e.target.id === 'lightbox-modal') closeLightbox();
});
</script>


{% include gallery  %}

{% include gallery id="gallery2021" caption=" " %}

{% include gallery id="gallery2021mocks" caption=" " %}

{% include feature_row %}

