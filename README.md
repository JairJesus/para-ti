# para-ti
// Función para revelar momentos
function revelarMomento(element) {
    const reveal = element.querySelector('.moment-reveal');
    reveal.classList.toggle('hidden');
    
    if (!reveal.classList.contains('hidden')) {
        reveal.classList.add('show');
        element.style.transform = 'scale(1.05)';
    } else {
        reveal.classList.remove('show');
        element.style.transform = 'scale(1)';
    }
}

// Función para ir a la segunda pantalla
function goToStageTwo() {
    const intro = document.querySelector('.intro-screen');
    const second = document.querySelector('.second-screen');

    intro.classList.add('hidden');
    second.classList.remove('hidden');
    second.classList.add('fade-in');
    window.scrollTo({ top: 0, behavior: 'smooth' });
    createPetals(20); // Efecto de pétalos al cambiar pantalla
}

// Función para continuar después del video
function continueToGift() {
    const second = document.querySelector('.second-screen');
    const game = document.querySelector('.game-screen');

    second.classList.add('hidden');
    game.classList.remove('hidden');
    game.classList.add('fade-in');
    window.scrollTo({ top: 0, behavior: 'smooth' });
    startGame();
}

// Función para cerrar modal
function cerrarModal() {
    document.getElementById('modal').classList.add('hidden');
}

// Botón que se mueve si intentas pulsar "No continuar"
function moveAvoidButton() {
    const avoidButton = document.getElementById('avoid-button');
    const maxX = 120;
    const maxY = 30;
    const randomX = Math.floor(Math.random() * maxX - maxX / 2);
    const randomY = Math.floor(Math.random() * maxY - maxY / 2);
    avoidButton.style.transform = `translate(${randomX}px, ${randomY}px)`;
}

// Función para reproducir canciones (simulado)
function playSong(songId) {
    const songs = {
        song1: '🎵 Reproduciendo música romántica...',
        song2: '🎵 La música suena en tu corazón...',
        song3: '🎵 Esta canción es para ti...'
    };
    
    alert(songs[songId]);
    createHearts(20);
}

// Función para sorpresa especial
function sorpresa() {
    alert('¡Te amo! 💕\n\nPara Milove.');
    createHearts(50);
    playConfetti();
}

// Crear corazones flotantes
function createHearts(count) {
    const container = document.body;
    
    for (let i = 0; i < count; i++) {
        const heart = document.createElement('div');
        heart.innerHTML = '❤️';
        heart.style.position = 'fixed';
        heart.style.left = Math.random() * 100 + '%';
        heart.style.top = '-50px';
        heart.style.fontSize = (Math.random() * 20 + 20) + 'px';
        heart.style.opacity = '1';
        heart.style.zIndex = '999';
        heart.style.pointerEvents = 'none';
        heart.style.animation = `float-up ${Math.random() * 3 + 3}s ease-in infinite`;
        
        container.appendChild(heart);
        
        setTimeout(() => {
            heart.remove();
        }, 4000);
    }
}

// Animación de confeti
function playConfetti() {
    const colors = ['🎉', '✨', '💖', '🌟', '💝'];
    
    for (let i = 0; i < 30; i++) {
        const confetti = document.createElement('div');
        confetti.innerHTML = colors[Math.floor(Math.random() * colors.length)];
        confetti.style.position = 'fixed';
        confetti.style.left = Math.random() * 100 + '%';
        confetti.style.top = '50%';
        confetti.style.fontSize = (Math.random() * 20 + 15) + 'px';
        confetti.style.zIndex = '999';
        confetti.style.pointerEvents = 'none';
        confetti.style.opacity = '1';
        
        const angle = (Math.random() * Math.PI * 2);
        const velocity = Math.random() * 8 + 5;
        const vx = Math.cos(angle) * velocity;
        const vy = Math.sin(angle) * velocity;
        
        let x = parseFloat(confetti.style.left);
        let y = parseFloat(confetti.style.top);
        let rotation = 0;
        
        document.body.appendChild(confetti);
        
        const animate = () => {
            x += vx;
            y += vy;
            rotation += Math.random() * 20;
            
            confetti.style.left = x + 'px';
            confetti.style.top = y + 'px';
            confetti.style.transform = `rotate(${rotation}deg)`;
            confetti.style.opacity = Math.max(0, 1 - (y / window.innerHeight));
            
            if (y < window.innerHeight && confetti.style.opacity > 0) {
                requestAnimationFrame(animate);
            } else {
                confetti.remove();
            }
        };
        
        animate();
    }
}

// Cerrar modal al hacer click fuera
window.onclick = function(event) {
    const modal = document.getElementById('modal');
    if (event.target == modal) {
        cerrarModal();
    }
}

// Variables del minijuego
let canvas, ctx;
let snoopy = { x: 200, y: 250, width: 40, height: 40 };
let hearts = [];
let score = 0;
let gameRunning = false;

// Función para iniciar el minijuego
function startGame() {
    canvas = document.getElementById('game-canvas');
    ctx = canvas.getContext('2d');
    score = 0;
    hearts = [];
    gameRunning = true;
    document.getElementById('score').textContent = 'Corazones atrapados: 0/10';

    // Reproducir música de fondo
    const bgMusic = document.getElementById('bg-music');
    bgMusic.play().catch(() => {}); // Ignorar errores si no se puede reproducir

    // Event listeners para movimiento
    document.addEventListener('keydown', moveSnoopy);

    // Generar corazones cada cierto tiempo
    setInterval(createHeart, 1000);

    // Iniciar loop del juego
    gameLoop();
}

// Función para mover a Snoopy
function moveSnoopy(e) {
    if (!gameRunning) return;
    const speed = 10;
    if (e.key === 'ArrowLeft' && snoopy.x > 0) snoopy.x -= speed;
    if (e.key === 'ArrowRight' && snoopy.x < canvas.width - snoopy.width) snoopy.x += speed;
    if (e.key === 'ArrowUp' && snoopy.y > 0) snoopy.y -= speed;
    if (e.key === 'ArrowDown' && snoopy.y < canvas.height - snoopy.height) snoopy.y += speed;
}

// Función para crear corazones
function createHeart() {
    if (!gameRunning) return;
    const heart = {
        x: Math.random() * (canvas.width - 20),
        y: 0,
        width: 20,
        height: 20,
        speed: Math.random() * 3 + 2
    };
    hearts.push(heart);
}

// Función del loop del juego
function gameLoop() {
    if (!gameRunning) return;

    // Limpiar canvas
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    // Dibujar fondo
    ctx.fillStyle = '#f0f0f0';
    ctx.fillRect(0, 0, canvas.width, canvas.height);

    // Dibujar Snoopy (emoji)
    ctx.font = '30px Arial';
    ctx.fillText('🐶', snoopy.x, snoopy.y + 30);

    // Dibujar y actualizar corazones
    hearts.forEach((heart, index) => {
        heart.y += heart.speed;
        ctx.font = '20px Arial';
        ctx.fillText('❤️', heart.x, heart.y + 20);

        // Chequear colisión con Snoopy
        if (heart.x < snoopy.x + snoopy.width &&
            heart.x + heart.width > snoopy.x &&
            heart.y < snoopy.y + snoopy.height &&
            heart.y + heart.height > snoopy.y) {
            hearts.splice(index, 1);
            score++;
            document.getElementById('score').textContent = `Corazones atrapados: ${score}/10`;
            createHearts(5); // Efecto de corazones al atrapar
            // Reproducir sonido de atrapar
            document.getElementById('catch-sound').play().catch(() => {});
            if (score >= 10) {
                gameRunning = false;
                goToFinal();
            }
        }

        // Remover corazones que salen de pantalla
        if (heart.y > canvas.height) {
            hearts.splice(index, 1);
        }
    });

    requestAnimationFrame(gameLoop);
}

// Función para ir a la pantalla final
function goToFinal() {
    const game = document.querySelector('.game-screen');
    const final = document.querySelector('.final-screen');

    game.classList.add('hidden');
    final.classList.remove('hidden');
    final.classList.add('fade-in');
    window.scrollTo({ top: 0, behavior: 'smooth' });
    createHearts(50);
    createPetals(30); // Pétalos al ganar
    animateMessage();
    // Reproducir sonido de victoria
    document.getElementById('win-sound').play().catch(() => {});
}

// Función para mostrar el modal final
function showFinalModal() {
    const modal = document.getElementById('modal');
    document.getElementById('modal-title').textContent = 'Te amo, Mirella 💜';
    document.getElementById('modal-text').textContent = 'Eres la persona mas importante para mi, mi niña de ojos bellos (ojo de panda) te amo y te seguire amando una y otra vez mas, te amo mirella!';
    modal.classList.remove('hidden');
}

// Función para animar el mensaje letra por letra
function animateMessage() {
    const messageElement = document.querySelector('.final-screen .message');
    const fullText = messageElement.innerHTML.replace(/<br>/g, '\n').replace(/<br\s*\/?>/g, '\n');
    messageElement.innerHTML = '';
    let index = 0;
    const interval = setInterval(() => {
        if (index < fullText.length) {
            if (fullText[index] === '\n') {
                messageElement.innerHTML += '<br>';
            } else {
                messageElement.innerHTML += fullText[index];
            }
            index++;
        } else {
            clearInterval(interval);
        }
    }, 50); // Velocidad de escritura
}

// Cerrar modal al hacer click fuera
window.onclick = function(event) {
    const modal = document.getElementById('modal');
    if (event.target == modal) {
        cerrarModal();
    }
    const easterModal = document.getElementById('easter-modal');
    if (event.target == easterModal) {
        cerrarEasterModal();
    }
}

// Función para cerrar easter modal
function cerrarEasterModal() {
    document.getElementById('easter-modal').classList.add('hidden');
}

// Variables para easter egg
let keySequence = [];
const secretCode = ['m', 'i', 'r', 'e'];

// Listener para easter egg
document.addEventListener('keydown', (e) => {
    keySequence.push(e.key.toLowerCase());
    if (keySequence.length > secretCode.length) {
        keySequence.shift();
    }
    if (keySequence.join('') === secretCode.join('')) {
        document.getElementById('easter-modal').classList.remove('hidden');
        createHearts(100);
        playConfetti();
        keySequence = []; // Reset
    }
});

// Función para crear pétalos
function createPetals(count) {
    const container = document.body;
    
    for (let i = 0; i < count; i++) {
        const petal = document.createElement('div');
        petal.innerHTML = '🌸';
        petal.style.position = 'fixed';
        petal.style.left = Math.random() * 100 + '%';
        petal.style.top = '-50px';
        petal.style.fontSize = (Math.random() * 20 + 15) + 'px';
        petal.style.opacity = '0.8';
        petal.style.zIndex = '999';
        petal.style.pointerEvents = 'none';
        petal.style.animation = `petal-fall ${Math.random() * 5 + 5}s linear infinite`;
        
        container.appendChild(petal);
        
        setTimeout(() => {
            petal.remove();
        }, 10000);
    }
}

// Efecto parallax en el fondo
window.addEventListener('scroll', () => {
    const scrolled = window.pageYOffset;
    const background = document.querySelector('.background');
    background.style.transform = `translateY(${scrolled * 0.5}px)`;
});

// Evitar el botón cuando el cursor intente pulsarlo
const avoidButton = document.getElementById('avoid-button');
if (avoidButton) {
    avoidButton.addEventListener('mouseenter', moveAvoidButton);
    avoidButton.addEventListener('click', moveAvoidButton);
    avoidButton.addEventListener('touchstart', moveAvoidButton);
}

// Agregar keyframe de animación
const style = document.createElement('style');
style.textContent = `
    @keyframes float-up {
        0% {
            transform: translateY(0) translateX(0);
            opacity: 1;
        }
        100% {
            transform: translateY(-${window.innerHeight}px) translateX(${Math.random() * 100 - 50}px);
            opacity: 0;
        }
    }
`;
document.head.appendChild(style);

// Exponer funciones globales para los onclick inline
window.goToStageTwo = goToStageTwo;
window.continueToGift = continueToGift;
window.cerrarModal = cerrarModal;
window.cerrarEasterModal = cerrarEasterModal;
window.showFinalModal = showFinalModal;

// Evento para cambiar tema (opcional, comentado para no afectar)
document.addEventListener('keydown', (e) => {
    if (e.key === 'ArrowUp' && e.ctrlKey) {
        createHearts(30);
    }
});
