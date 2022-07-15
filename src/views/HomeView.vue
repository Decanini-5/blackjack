<template>
  <main>
    <div v-if="currentStep == 'betting' || currentStep == 'playerTurn'">
      <BetPopUp v-if="currentStep == 'betting'" :text="betPopUpText" @betted="getBet" />
      <OptionPopUp v-if="currentStep == 'playerTurn'" :cardSum="playerSum" @hit="hitCard" @stay="playerStay" />
    </div>
    <div v-else>
      <ResultPopUp :cardSum="playerSum" :dSum="dealerSum" :showDealerSum="currentStep == 'dealerTurn' || currentStep == 'roundOver'" :text="resPopUpText" @replay="newRound" />
    </div>
    <div class="container">
      <h1>Royal BlackJack</h1>
      <div class="balance-box">${{ balance }}</div>
      <div v-if="currentStep != 'betting'" class="dealer-hand">
        <DeckCard v-if="currentStep == 'dealerTurn' || currentStep == 'roundOver'" :suit="dealerHand[1].suit" :rank="dealerHand[1].rank" />
        <div v-else class="dealer-deck"></div>
        <DeckCard :suit="dealerHand[0].suit" :rank="dealerHand[0].rank" />
        <DeckCard
          v-if="dealerHand.length >= 3"
          :suit="dealerHand[2].suit"
          :rank="dealerHand[2].rank"
        />
        <DeckCard
          v-if="dealerHand.length >= 4"
          :suit="dealerHand[3].suit"
          :rank="dealerHand[3].rank"
        />
        <DeckCard
          v-if="dealerHand.length >= 5"
          :suit="dealerHand[4].suit"
          :rank="dealerHand[4].rank"
        />
      </div>
      <div class="bet-box">${{ betAmount }}</div>
      <div v-if="currentStep != 'betting'" class="player-hand">
        <DeckCard :suit="playerHand[0].suit" :rank="playerHand[0].rank" />
        <DeckCard :suit="playerHand[1].suit" :rank="playerHand[1].rank" />
        <DeckCard
          v-if="playerHand.length >= 3"
          :suit="playerHand[2].suit"
          :rank="playerHand[2].rank"
        />
        <DeckCard
          v-if="playerHand.length >= 4"
          :suit="playerHand[3].suit"
          :rank="playerHand[3].rank"
        />
        <DeckCard
          v-if="playerHand.length >= 5"
          :suit="playerHand[4].suit"
          :rank="playerHand[4].rank"
        />
      </div>
      <div class="action-btns">
        <button class="quit-btn" @click="quitGame">QUIT GAME</button>
      </div>
    </div>
  </main>
</template>

<script>
import DeckCard from '@/components/DeckCard.vue';
import BetPopUp from '@/components/BetPopUp.vue';
import OptionPopUp from '@/components/OptionPopUp.vue';
import ResultPopUp from '@/components/ResultPopUp.vue';

export default {
  name: 'HomeView',
  components: {
    DeckCard,
    BetPopUp,
    OptionPopUp,
    ResultPopUp,
  },
  data() {
    return {
      newDeck: [],
      playerHand: [],
      dealerHand: [],
      playerHandValues: [],
      balance: 100,
      betAmount: 0,
      currentStep: 'betting',
      betPopUpText: '',
      resPopUpText: '',
    };
  },
  computed: {
    playerSum() {
      return this.getSum(this.playerHand);
    },
    dealerSum() {
      return this.getSum(this.dealerHand);
    },
    dealerMustDraw() {
      return this.dealerSum <= this.playerSum;
    },
  },
  methods: {
    createDeck() {
      const deck = [];
      const SUITS = ['Hearts', 'Spades', 'Clubs', 'Diamonds'];
      const RANKS = ['Ace','2','3','4','5','6','7','8','9','10','Jack','Queen','King']
      const VALUES = {'Ace':1,'2':2,'3':3,'4':4,'5':5,'6':6,'7':7,'8':8,'9':9,'10':10,'Jack':10,'Queen':10,'King':10};
      
      for (const s of SUITS) {
        for (const r of RANKS) {
          let card = { suit: '', rank: '', value: '' };

          card['suit'] = s;
          card['rank'] = r;
          card['value'] = VALUES[r];

          deck.push(card);
        }
      }
      for (let i = deck.length - 1; i > 0; i--) {
        let j = Math.floor(Math.random() * (i + 1));
        let temp = deck[i];
        deck[i] = deck[j];
        deck[j] = temp;
      }
      return deck;
    },
    createPlayerHand() {
      let card1 = this.newDeck.pop();
      let card2 = this.newDeck.pop();
      const initialHand = [card1, card2];

      return initialHand;
    },
    createDealerHand() {
      let card1 = this.newDeck.pop();
      let card2 = this.newDeck.pop();
      const initialHand = [card1, card2];

      return initialHand;
    },
    hitCard() {
      let card = this.newDeck.pop();
      this.playerHand.push(card);
      this.checkWin();
    },
    playerStay() {
      this.currentStep = 'dealerTurn';
      this.checkWin();
      while (this.dealerMustDraw) {
        let card = this.newDeck.pop();
        this.dealerHand.push(card);
        if (this.checkWin()) {
          break;
        }
      }
      if (this.dealerSum > 21) {
        this.resPopUpText = 'DEALER BUSTED! You have won this round.';
        this.balance += this.betAmount;
        this.betAmount = 0;
        this.currentStep = 'roundOver';
      } else {
        this.resPopUpText = 'The dealer has the best score. He has won this round.';
        this.balance += this.betAmount;
        this.betAmount = 0;
        this.currentStep = 'roundOver';
      }
    },
    getBet() {
      if (document.querySelector('input').value == 0) {
        alert('Please enter a bet amount.');
      } else {
        this.betAmount = Number(document.querySelector('input').value);
        this.newRound();
        if (!this.checkBlackJack()) {
          this.currentStep = 'playerTurn';
        }
      }
    },
    checkBlackJack() {
      const isBlackJack = this.playerSum == 21 && this.playerHand.length == 2;
      if (isBlackJack) {
        this.resPopUpText = 'YOU SCORED 21 BLACKJACK! You have won this round!';
        this.balance += this.betAmount;
        this.betAmount = 0;
        this.currentStep = 'roundOver';
      }
      return isBlackJack;
    },
    getSum(hand) {
      const hasAce = hand.some(card => {
        if (card.rank === 'Ace') {
          return true;
        }
      });
      let sum = 0;
      for (let i = 0; i < hand.length; i++) {
        sum += hand[i].value;
      }
      if (hasAce && sum <= 11) {
        sum += 10;
      }
      return sum;
    },
    checkWin() {
      if (this.dealerSum == 21 && this.currentStep == 'dealerTurn') {
        this.resPopUpText = 'DEALER SCORED 21 BLACKJACK! He has won this round!';
        this.balance -= this.betAmount;
        this.betAmount = 0;
        this.currentStep = 'roundOver';
      } else if (this.dealerSum > 21) {
        this.resPopUpText = 'DEALER BUSTED! You have won this round.';
        this.balance += this.betAmount;
        this.betAmount = 0;
        this.currentStep = 'roundOver';
      } else if (this.playerSum == 21) {
        this.resPopUpText = 'YOU SCORED 21 BLACKJACK! You have won this round!';
        this.balance += this.betAmount;
        this.betAmount = 0;
        this.currentStep = 'roundOver';
      } else if (this.playerSum > 21) {
        this.resPopUpText = 'YOU BUSTED! The dealer has won this round.';
        this.balance -= this.betAmount;
        this.betAmount = 0;
        this.currentStep = 'roundOver';
      } else {
        this.resPopUpText = 'The dealer must beat your score to win.\nHis cards sum: ';
      }
    },
    newRound() {
      console.log(this.currentStep);
      if (this.currentStep == 'roundOver') {
        this.currentStep = 'betting';
      } else {
        this.currentStep = 'playerTurn';
      }
      this.newDeck = this.createDeck();
      this.playerHand = this.createPlayerHand();
      this.dealerHand = this.createDealerHand();
      //this.dealerHand = [{suit: 'Clubs', rank : 'Ace', value : 1},{suit : 'Clubs', rank : 'King', value: 10}];
      this.betPopUpText = '';
      console.log(this.newDeck);
    },
    quitGame() {
      if (confirm("Are you sure you want to exit?") == true) {
        window.location.reload();
      }
    },
  },
  created() {
    this.betPopUpText = 'Welcome to Royal BlackJack!';
  },
};
</script>

<style scoped>
.container {
  background-color: darkslategrey;
  width: 620px;
  height: 80%;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
.balance-box {
  background-color: whitesmoke;
  border-radius: 5px;
  width: 100px;
  height: 40px;
  position: absolute;
  top: 20px;
  right: 20px;
  text-align: center;
  line-height: 40px;
  font-size: 150%;
}
.dealer-hand {
  padding-left: 3%;
  display: flex;
}
.player-hand {
  position: absolute;
  display: flex;
  right: 5%;
  bottom: 15%;
}
h1 {
  color:whitesmoke;
  padding-left: 3%;
}
.dealer-deck {
  border: 1px grey solid;
  border-radius: 7px;
  width: 70px;
  height: 100px;
  position: relative;
  display: flex;
  margin: 0.5em;
  background-image: url(../assets/nature-pattern.jpg);
}
.player-deck {
  border: 1px grey;
  border-radius: 7px;
  width: 70px;
  height: 100px;
  margin: 0.5em;
  background-image: url(../assets/nature-pattern.jpg)
}
.bet-box {
  position: absolute;
  width: 80px;
  height: 80px;
  top: 90px;
  right: 30px;
  background: url(../assets/casino-chip.png);
  background-position: center;
  background-size: cover;
  background-repeat: no-repeat;
  text-align: center;
  line-height: 80px;
  font-size: 150%;
}
.action-btns {
  position: absolute;
  width: 100%;
  bottom: 3%;
  display: flex;
  justify-content: space-around;
}
.quit-btn {
  background-color: pink;
  border-radius: 5px;
  border: none;
  position: relative;
  width: 100px;
  margin: 0.8em 0 0.8em;
  padding: 7px 0 7px;
  text-align: center;
}
</style>