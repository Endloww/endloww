<canvas id="mouse-trail"></canvas>
<div class="button-container">
    <a href="index.html"><img class="rounded" src="img/logo/AUBL.png" width="50" alt="Logo"></a>
    <button id="toggleButton" class="round-button">
        <img src="img/magic.png" alt="Baguette magique">
    </button>
    <button id="increaseButton" class="size-adjust-button">
        <img src="img/+.png" alt="+">
    </button>
    <button id="decreaseButton" class="size-adjust-button">
        <img src="img/-.png" alt="-">
    </button>
    <input type="color" id="colorPicker" value="#b03be6" class="select-color">
</div>  

<style> .button-container {
    display: flex; /* Utilisation de flexbox */
    align-items: center; /* Centre verticalement les éléments */
  }
  .size-adjust-button {
    position: relative;
    background-color: #393939;
    border-radius: 4em;
    color: white;
    margin-left: 10px;
    cursor: pointer;
    user-select: none;
    text-align: center;
    text-decoration: none;
    cursor: pointer;
    transition-duration: 0.4s;
    -webkit-transition-duration: 0.4s; /* Safari */
    width: 30px;
    height: 30px;
    box-shadow: 5px 5px 10px 0 rgba(0, 0, 0, 0.5);
    display: flex; /* Utilisation de Flexbox */
    justify-content: center; /* Centre le contenu horizontalement */
    align-items: center; /* Centre le contenu verticalement */
  }
  
  .size-adjust-button img {
    max-width: 100%;
    max-height: 100%;
    width: auto;
    height: auto;
  }
  
  .size-adjust-button:hover {
    transition-duration: 0.1s;
    background-color: #693784;
    transform: scale(1.1);
  }
  
  .size-adjust-button:after {
    content: "";
    display: block;
    position: absolute;
    border-radius: 4em;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    opacity: 0;
    transition: all 0.5s;
    box-shadow: 0 0 10px 40px #8647a3;
  }
  
  .size-adjust-button:active:after {
    box-shadow: 0 0 0 0 #8647a3;
    position: absolute;
    border-radius: 4em;
    left: 0;
    top: 0;
    opacity: 1;
    transition: 0s;
  }
  
  .size-adjust-button:active {
    top: 1px;
  }
.select-color{
    border-radius: 5px;
    margin-left: 10px;
    background-color: #393939;
    width: 30px; 
    height: 30px;
    box-shadow: 5px 5px 10px 0 rgba(0,0,0,0.5);
    transition: transform 0.3s ease;
  }
  .select-color:hover {
    transform: scale(1.1); /* Zoom au survol à 120% de la taille d'origine */
    background-color: #693784;
  } 
</style>

<script>
  
document.addEventListener('DOMContentLoaded', function() {
    const canvas = document.getElementById('mouse-trail');
    const ctx = canvas.getContext('2d');
    let particles = [];
    let mouseX = 0;
    let mouseY = 0;
    let isDrawing = false;
    let particleSize = 3;
    let selectedColor = "#b03be6"; // Couleur par défaut

    const increaseButton = document.getElementById('increaseButton');
    const decreaseButton = document.getElementById('decreaseButton');
    const colorPicker = document.getElementById('colorPicker');
     // Ajoutez les classes pour cacher initialement les boutons et l'input
    increaseButton.classList.add('hide');
    decreaseButton.classList.add('hide');
    colorPicker.classList.add('hide');
  
    document.getElementById('toggleButton').addEventListener('click', function() {
      isDrawing = !isDrawing; // Inversion de l'état actuel de la trainée
  
       if (isDrawing) {
      increaseButton.classList.add('show');
      decreaseButton.classList.add('show');
      colorPicker.classList.add('show');
    } else {
      increaseButton.classList.remove('show');
      decreaseButton.classList.remove('show');
      colorPicker.classList.remove('show');
    }

    if (!isDrawing) {
      particles = [];
    }
  });
  
    document.getElementById('increaseButton').addEventListener('click', function() {
      if (isDrawing) {
        particleSize += 1; // Augmenter la taille des particules
      }
    });
  
    document.getElementById('decreaseButton').addEventListener('click', function() {
      if (isDrawing && particleSize > 1) {
        particleSize -= 1; // Diminuer la taille des particules (si elle est supérieure à 1)
      }
    });
  
    document.getElementById('colorPicker').addEventListener('input', function() {
      selectedColor = this.value; // Mettre à jour la couleur sélectionnée
    });
  
    function updateCanvasSize() {
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;
    }
  
    function createParticle(x, y) {
      if (isDrawing) {
        for (let i = 0; i < 3; i++) {
          const particle = {
            x,
            y,
            vx: Math.random() * 1 - 0.5,
            vy: Math.random() * 1 - 0.5,
            size: particleSize,
            color: selectedColor,
            opacity: Math.random() * 0.5 + 0.5
          };
          particles.push(particle);
        }
      }
    }
  
    function moveParticles() {
      if (isDrawing) {
        for (let i = 0; i < particles.length; i++) {
          const p = particles[i];
          p.x += p.vx;
          p.y += p.vy;
          p.size -= 0.03;
        }
        particles = particles.filter(p => p.size > 0);
      }
    }
  
    function drawParticles() {
      if (isDrawing) {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        for (let i = 0; i < particles.length; i++) {
          const p = particles[i];
          const gradient = ctx.createRadialGradient(p.x, p.y, 0, p.x, p.y, p.size);
          gradient.addColorStop(0, p.color);
          gradient.addColorStop(1, 'transparent');
  
          ctx.fillStyle = gradient;
          ctx.fillRect(p.x - p.size / 2, p.y - p.size / 2, p.size, p.size);
        }
      }
    }
  
    document.addEventListener('mousemove', function(e) {
      const rect = canvas.getBoundingClientRect();
      mouseX = e.clientX - rect.left;
      mouseY = e.clientY - rect.top;
  
      createParticle(mouseX, mouseY); // Utilisez la couleur sélectionnée pour créer les particules
    });
  
    function animate() {
      updateCanvasSize();
      moveParticles();
      drawParticles();
      requestAnimationFrame(animate);
    }
  
    animate();
  });
  
</script>
