<script>
  export default {
    data() {
      return {
        // トランプの山札
        allCards: [
          '♠1', '♠2', '♠3', '♠4', '♠5', '♠6', '♠7', '♠8', '♠9', '♠10', '♠11', '♠12', '♠13',
          '♣1', '♣2', '♣3', '♣4', '♣5', '♣6', '♣7', '♣8', '♣9', '♣10', '♣11', '♣12', '♣13',
          '♥1', '♥2', '♥3', '♥4', '♥5', '♥6', '♥7', '♥8', '♥9', '♥10', '♥11', '♥12', '♥13',
          '♦1', '♦2', '♦3', '♦4', '♦5', '♦6', '♦7', '♦8', '♦9', '♦10', '♦11', '♦12', '♦13'
        ],

        userCards: [
          {key:0, cards:[]}, // ディーラー
          {key:1, cards:[]}, // プレイヤー1
        ],
        
        // 各プレイヤーのチップを管理する配列
        tips: [
          {key:1, number:100}, // プレイヤー1
        ],
      }
    },
    methods: {
      start() {
        this.$refs.startButton.style.display = "none";
        this.drawCards(0);
        this.drawCards(1);
        this.$refs.hitButton.style.display = "inline";
        this.$refs.standButton.style.display = "inline";
      },
      drawCards(key) {
        const randomIndex = Math.floor(Math.random() * this.allCards.length);
        const randomCard = this.allCards[randomIndex];
        this.allCards.splice(randomIndex, 1);
        const targetObj = this.userCards.find(card => card.key === key);
        targetObj.cards.push(randomCard);
      },
      hit() {
        this.drawCards(1);
        if(this.user1CardsNum > 21) {
          this.end();
        }
      },
      end() {
        this.$refs.hitButton.style.display = "none";
        this.$refs.standButton.style.display = "none";
        while(this.dealerCardsNum < 17) {
          this.drawCards(0);
        }
        this.$refs.restartButton.style.display = "inline";

        let resultText = "";
        if(
          (this.user1CardsNum > 21 && this.dealerCardsNum <= 21)
          ||
          (this.user1CardsNum < this.dealerCardsNum && this.dealerCardsNum <= 21 && this.user1CardsNum <= 21)
        ) {
          resultText = "YOU LOSE";
        }else if(
          (this.user1CardsNum <= 21 && this.dealerCardsNum > 21)
          ||
          (this.user1CardsNum > this.dealerCardsNum && this.user1CardsNum <= 21 && this.dealerCardsNum <= 21)
        ) {
          resultText = "YOU WIN";
        }else if(
          (this.user1CardsNum === this.dealerCardsNum)
          ||
          (this.user1CardsNum > 21 && this.dealerCardsNum > 21)
        ) {
          resultText = "DRAW";
        }
        this.$refs.resultP.textContent = resultText;
      }
    },

    computed: {
      dealerCards() {
        const dealerObj = this.userCards.find(card => card.key === 0);
        return dealerObj.cards;
      },
      user1Cards() {
        const user1Cards = this.userCards.find(card => card.key === 1);
        return user1Cards.cards;
      },
      dealerCardsNum() {
        const dealerObj = this.userCards.find(card => card.key === 0);
        let rtnNum = 0;
        for(let i=0; i<dealerObj.cards.length; i++) {
          const cardNumber = dealerObj.cards[i].substr(1);
          rtnNum += Number(cardNumber);
        }
        return rtnNum;
      },
      user1CardsNum() {
        const user1Obj = this.userCards.find(card => card.key === 1);
        let rtnNum = 0;
        for(let i=0; i<user1Obj.cards.length; i++) {
          const cardNumber = user1Obj.cards[i].substr(1);
          rtnNum += Number(cardNumber);
        }
        return rtnNum;
      },
    },
  }
</script>

<template>
  <div style="display: flex;">
    <h3>ディーラーの手札</h3>
    <p>合計：{{ dealerCardsNum }}</p>
    <ul>
      <li v-for="card in dealerCards">
        {{ card }}
      </li>
    </ul>
  </div>

  <div style="display: flex;">
    <h3>あなたの手札</h3>
    <p>合計：{{ user1CardsNum }}</p>
    <ul>
      <li v-for="card in user1Cards">
        {{ card }}
      </li>
    </ul>
  </div>

  <p ref="resultP"></p>

  <a @click="start" ref="startButton">ゲームスタート</a>
  <a href="#/" ref="restartButton" style="display: none">リスタート</a>
  <a @click="hit" ref="hitButton" style="display: none">ヒット</a>
  <a @click="end" ref="standButton" style="display: none">スタンド</a>
</template>