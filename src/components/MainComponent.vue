<template>
    <div id="topSection" class="top-section a-ct">
        <div class="top-title">
            <p>백하얀이 생일</p>
        </div>
        <div class="top-content">
            <h1>생일파티에 초대합니당</h1>
            <p>벌써 1년이 지났다요</p>
            <p>생일을 축하합니다</p>
        </div>
        <div class="top-footer">
            <p>2024. 12. 7. SAT. PM 1:30</p>
            <p>경상남도 사천시 사천읍</p>
        </div>
    </div>
    <div id="fixedHeader" class="fixed-header">
        <h2>백하얀의 첫번째 생일잔치</h2>
    </div>
    <main>
        <section class="content vh80 a-ct t-ct dp-flx-c">
            <div class="title-area bg-wht">
                <h3>인사말</h3>
                <h2 class="hide">소제목</h2>
            </div>
            <div class="contents-area">
                <p>안녕하세요</p>
                <p>하얀이를 사랑해주셔서</p>
                <p>대단히 감사합니다</p>
            </div>
        </section>
        <section class="content">
            <div class="title-area">
                <h3>Phone</h3>
                <h2 class="hide">연락처</h2>
            </div>
            <div class="contents-area dp-flx-r j-ct t-ct">
                <div class="wd50 dp-flx-c">
                    <p>아빠</p>
                    <p>백상원</p>
                    <p>010 4864 8958</p>
                    <br>
                    <p>오빠</p>
                    <p>백빰이</p>
                    <p>010 1234 5678</p>
                </div>
                <div class="wd50 dp-flx-c">
                    <p>엄마</p>
                    <p>김민정</p>
                </div>
            </div>
        </section>
        <section class="content">
            <div class="title-area">
                <h3>Gallery</h3>
                <h2 class="hide">사진</h2>
            </div>
            <div class="contents-area">
                <div class="slider-container">
                    <button class="slide-btn prev-btn">◀︎</button>
                    <div class="slider">
                        <Card v-for="(card, idx) in cards" :key="idx" :content="card.content" :img="card.img"/>
                    </div>
                    <button class="slide-btn next-btn">▶︎</button>
                </div>
            </div>
        </section>
    </main>
</template>
<script>
import Card from './CardComponent.vue'
import images from '../store/img.json'

export default {
  components: {
    Card
  },
  data () {
    return {
      currentIdx: 0,
      startX: 0,
      isDragging: false,
      currentTranslate: 0,
      prevTranslate: 0,
      cards: [
        { content: 'Photo 1' },
        { content: 'Photo 2' },
        { content: 'Photo 3' }
      ]
    }
  },
  methods: {
    // scroll event
    handleScroll () {
      const fixedHeader = document.getElementById('fixedHeader')
      const main = document.getElementsByTagName('main')[0]

      if (window.scrollY > window.innerHeight) {
        fixedHeader.classList.add('show-header')
        main.classList.add('mt10vh', 'pt10vh')
      } else {
        fixedHeader.classList.remove('show-header')
        main.classList.remove('mt10vh', 'pt10vh')
      }
    },
    // slider event
    nextBtnEvent () {
      this.currentIdx++
      this.slideCards()
    },
    prevBtnEvent () {
      this.currentIdx--
      this.slideCards()
    },
    touchEvent (e) {
      console.log(e)
      console.log(e.type)
    },
    slideCards () {
      const slider = document.querySelector('.slider')

      const cardWidth = slider.firstElementChild.clientWidth + 20

      const maxIdx = slider.children.length - Math.floor(slider.offsetWidth / cardWidth)
      const maxWdt = slider.scrollWidth

      if (this.currentIdx > maxIdx) {
        this.currentIdx = maxIdx
      } else if (this.currentIdx < 0) {
        this.currentIdx = 0
      } else if (Math.floor(this.currentIdx * cardWidth) > maxWdt) {
        this.currentIdx--
      }

      slider.style.transform = 'translateX(-' + (this.currentIdx * cardWidth) + 'px)'
      this.prevTranslate = -(this.currentIdx * cardWidth)
    },
    // 카드추가
    addCard () {
      this.cards.push({ content: `(New) Card ${this.cards.length + 1}` })
    }
  },
  mounted () {
    // 사진
    for (let i = 0; i < this.cards.length; i++) {
      this.cards[i].img = images.imgs[i]
    }

    // scroll event
    window.addEventListener('scroll', this.handleScroll)

    // slider event
    const slider = document.querySelector('.slider')
    const nextBtn = document.querySelector('.next-btn')
    const prevBtn = document.querySelector('.prev-btn')

    nextBtn.addEventListener('click', () => {
      this.currentIdx++
      this.slideCards()
    })
    prevBtn.addEventListener('click', () => {
      this.currentIdx--
      this.slideCards()
    })
    slider.addEventListener('touchstart', (e) => {
      this.startX = e.touches[0].clientX
      this.isDragging = true
      slider.style.transition = 'none'
    })
    slider.addEventListener('touchmove', (e) => {
      if (!this.isDragging) return

      const currentX = e.touches[0].clientX
      const diffX = currentX - this.startX

      this.currentTranslate = this.prevTranslate + diffX

      slider.style.transform = 'translateX(' + this.currentTranslate + 'px)'
    })
    slider.addEventListener('touchend', (e) => {
      this.isDragging = false

      if (this.startX === e.changeTouches[0].clientX) {
        slider.style.transition = 'transform 0.3s ease-in-out'
        return
      }

      const movedBy = this.currentTranslate - this.prevTranslate

      if (movedBy < -300) {
        this.currentIdx++
      } else if (movedBy > 300) {
        this.currentIdx--
      }

      this.slideCards()
      slider.style.transition = 'transform 0.3s ease-in-out'
    })
  },
  beforeUnmount () {
    window.removeEventListener('scroll', this.handleScroll)
  }
}
</script>
