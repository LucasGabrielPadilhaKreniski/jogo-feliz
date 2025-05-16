let x = 50;
let y = 250;
let velocidade = 0;
let gravidade = 0.5;
let noChao = false;
let carros = [];
let flores = [];
let faseAtual = 1; // 1 = cidade, 2 = campo
let janelas = [];
let estrelas = [];
let nuvens = [];
let sol;

function setup() {
  createCanvas(800, 400);
  
  // Criar janelas iluminadas
  for (let i = 0; i < 15; i++) {
    janelas.push({
      x: random(100, 700),
      y: random(100, 220),
      tamanho: random(15, 30),
      brilho: random(150, 255)
    });
  }
  
  // Criar estrelas
  for (let i = 0; i < 50; i++) {
    estrelas.push({
      x: random(0, 800),
      y: random(0, 200),
      tamanho: random(1, 3)
    });
  }
  
  // Criar carros
  for (let i = 0; i < 6; i++) {
    carros.push({
      x: random(100, 700),
      y: 320,
      largura: random(70, 120),
      altura: 40,
      cor: color(random(100, 200), random(50, 150), random(50, 150))
    });
  }
  
  // Criar flores
  for (let i = 0; i < 30; i++) {
    flores.push({
      x: random(0, 800),
      y: 350,
      tamanho: random(15, 25),
      cor: color(random(200, 255), random(0, 100), random(100, 200))
    });
  }
  
  // Criar nuvens
  for (let i = 0; i < 5; i++) {
    nuvens.push({
      x: random(0, 800),
      y: random(50, 150),
      velocidade: random(0.5, 1.5),
      tamanho: random(40, 80)
    });
  }
  
  // Criar sol
  sol = {
    x: 700,
    y: 100,
    tamanho: 60
  };
}

function draw() {
  // Fundo baseado na fase
  if (faseAtual === 1) {
    desenharCidadeNoturna();
  } else {
    desenharCampoSolar();
  }
  
  // Física do personagem
  velocidade += gravidade;
  y += velocidade;
  
  // Limitar personagem ao chão
  if (y > 250) {
    y = 250;
    velocidade = 0;
    noChao = true;
  } else {
    noChao = false;
  }
  
  // Desenhar personagem (rosto sorridente)
  desenharRosto(x, y);
  
  // Verificar colisão com carros (fase 1)
  if (faseAtual === 1) {
    for (let carro of carros) {
      fill(carro.cor);
      rect(carro.x, carro.y, carro.largura, carro.altura, 5);
      
      // Rodas do carro
      fill(0);
      circle(carro.x + 15, carro.y + carro.altura, 20);
      circle(carro.x + carro.largura - 15, carro.y + carro.altura, 20);
      
      if (x + 30 > carro.x && x < carro.x + carro.largura && y + 30 > carro.y) {
        // Volta para o início se bater em um carro
        x = 50;
        y = 250;
      }
    }
    
    // Verificar se chegou ao final da fase 1
    if (x > 750) {
      faseAtual = 2;
      x = 50;
    }
  }
  
  // Verificar se chegou ao final da fase 2
  if (faseAtual === 2 && x > 750) {
    fill(0);
    textSize(32);
    text("Chegou ao campo feliz!", 200, 200);
  }
}

function keyPressed() {
  // Pular quando espaço é pressionado
  if (key === ' ' && noChao) {
    velocidade = -12;
  }
  
  // Mover para direita/esquerda
  if (keyCode === RIGHT_ARROW) {
    x += 20;
  } else if (keyCode === LEFT_ARROW && x > 0) {
    x -= 20;
  }
}

function desenharRosto(x, y) {
  // Cabeça
  fill(255, 255, 0); // Amarelo
  circle(x + 15, y + 15, 30);
  
  // Olhos
  fill(0);
  circle(x + 8, y + 10, 4);
  circle(x + 22, y + 10, 4);
  
  // Sorriso
  noFill();
  stroke(0);
  strokeWeight(2);
  arc(x + 15, y + 18, 15, 10, 0, PI);
  noStroke();
}

function desenharCidadeNoturna() {
  background(10, 15, 30); // Noite profunda
  
  // Desenhar estrelas
  fill(255);
  for (let estrela of estrelas) {
    circle(estrela.x, estrela.y, estrela.tamanho);
  }
  
  // Desenhar prédios
  fill(30, 30, 40);
  rect(0, 150, 800, 180);
  
  // Desenhar janelas iluminadas
  for (let janela of janelas) {
    fill(255, 255, 0, janela.brilho);
    rect(janela.x, janela.y, janela.tamanho, janela.tamanho, 3);
  }
  
  // Desenhar chão
  fill(40, 40, 50);
  rect(0, 330, width, 70);
}

function desenharCampoSolar() {
  // Céu gradiente
  for (let i = 0; i < height; i++) {
    let inter = map(i, 0, height, 0, 1);
    let c = lerpColor(color(135, 206, 235), color(255, 255, 200), inter);
    stroke(c);
    line(0, i, width, i);
  }
  
  // Sol
  fill(255, 255, 0);
  circle(sol.x, sol.y, sol.tamanho);
  
  // Nuvens
  fill(255, 255, 255, 200);
  for (let nuvem of nuvens) {
    nuvem.x += nuvem.velocidade;
    if (nuvem.x > 850) nuvem.x = -50;
    ellipse(nuvem.x, nuvem.y, nuvem.tamanho, nuvem.tamanho/2);
    ellipse(nuvem.x + 20, nuvem.y - 10, nuvem.tamanho/1.5, nuvem.tamanho/3);
    ellipse(nuvem.x - 20, nuvem.y, nuvem.tamanho/1.5, nuvem.tamanho/3);
  }
  
  // Desenhar grama
  fill(50, 180, 70);
  rect(0, 330, width, 70);
  
  // Desenhar flores
  for (let flor of flores) {
    fill(flor.cor);
    // Caule
    stroke(50, 120, 50);
    strokeWeight(2);
    line(flor.x, flor.y, flor.x, flor.y + 20);
    noStroke();
    // Pétalas
    circle(flor.x, flor.y, flor.tamanho);
    fill(255, 255, 0); // Centro amarelo
    circle(flor.x, flor.y, flor.tamanho/3);
  }
}
