# Super-trunfo-3.0
class Card {
  constructor(name, attributes) {
    this.name = name;
    this.attributes = attributes;
    this.isTrump = false;
  }

  compare(attribute, opponentCard) {
    // Se esta carta é Super Trunfo, ganha automaticamente
    if (this.isTrump) return true;
    // Se a carta oponente é Super Trunfo, perde automaticamente
    if (opponentCard.isTrump) return false;
    // Comparação normal de atributos
    return this.attributes[attribute] > opponentCard.attributes[attribute];
  }
}

class Player {
  constructor(name) {
    this.name = name;
    this.hand = [];
    this.score = 0;
  }

  addCard(card) {
    this.hand.push(card);
  }

  playCard() {
    return this.hand.pop();
  }

  chooseAttribute() {
    // Escolhe aleatoriamente um atributo da primeira carta
    const attributes = Object.keys(this.hand[0].attributes);
    return attributes[Math.floor(Math.random() * attributes.length)];
  }
}

class SuperTrunfoGame {
  constructor() {
    this.players = [];
    this.createDeck();
  }

  createDeck() {
    this.cards = [
      new Card("Ferrari", { velocidade: 9, potencia: 8, peso: 5 }),
      new Card("Caminhão", { velocidade: 3, potencia: 10, peso: 10 }),
      new Card("Fusca", { velocidade: 4, potencia: 3, peso: 6 }),
      new Card("Moto", { velocidade: 8, potencia: 5, peso: 3 })
    ];

    // Define uma carta como Super Trunfo
    const trumpIndex = Math.floor(Math.random() * this.cards.length);
    this.cards[trumpIndex].isTrump = true;
    console.log(`Carta Super Trunfo: ${this.cards[trumpIndex].name}`);
  }

  startGame() {
    // Cria 2 jogadores
    this.players = [new Player("Jogador 1"), new Player("Jogador 2")];
    
    // Embaralha e distribui as cartas
    this.shuffleDeck();
    this.dealCards();
  }

  shuffleDeck() {
    for (let i = this.cards.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      [this.cards[i], this.cards[j]] = [this.cards[j], this.cards[i]];
    }
  }

  dealCards() {
    // Distribui as cartas igualmente
    for (let i = 0; i < this.cards.length; i++) {
      this.players[i % this.players.length].addCard(this.cards[i]);
    }
  }

  playRound() {
    const cardsInPlay = [];
    const attributesChosen = [];
    
    // Cada jogador joga uma carta
    for (const player of this.players) {
      const card = player.playCard();
      cardsInPlay.push({ player, card });
      console.log(`${player.name} jogou: ${card.name}`);

      const attribute = player.chooseAttribute();
      attributesChosen.push(attribute);
      console.log(`${player.name} escolheu atributo: ${attribute}`);
    }

    // Compara as cartas (usa o atributo escolhido pelo primeiro jogador)
    const winner = this.compareCards(
      cardsInPlay[0].card, 
      cardsInPlay[1].card, 
      attributesChosen[0]
    );

    if (winner) {
      winner.player.score++;
      console.log(`\n${winner.player.name} venceu a rodada!`);
      console.log(`Placar: ${this.players[0].name}: ${this.players[0].score} x ${this.players[1].name}: ${this.players[1].score}\n`);
    } else {
      console.log("\nRodada empatada!\n");
    }
  }

  compareCards(card1, card2, attribute) {
    if (card1.compare(attribute, card2)) {
      return { player: this.players[0], attribute };
    } else if (card2.compare(attribute, card1)) {
      return { player: this.players[1], attribute };
    }
    return null;
  }

  playGame() {
    this.startGame();
    
    // Joga rodadas até um jogador ficar sem cartas
    while (this.players.every(p => p.hand.length > 0)) {
      this.playRound();
    }
    
    // Anuncia o vencedor final
    const winner = this.players[0].score > this.players[1].score 
      ? this.players[0] 
      : this.players[1];
    
    console.log(`\n=== FIM DE JOGO ===`);
    console.log(`Vencedor: ${winner.name} com ${winner.score} vitórias!`);
  }
}

// Inicia o jogo
const game = new SuperTrunfoGame();
game.playGame();
