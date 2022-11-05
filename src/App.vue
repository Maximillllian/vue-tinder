<template>

    <main>
        <h1 class="content">
            <div class="hello">
                <span class="box"></span>
                <span class="hi">Привет, я</span>
            </div>
            <div class="animated-text">
                <span class="text"></span>
                <span class="cursor">_</span>
            </div>
            <div class="click-me">
                <button @click="onClick">Кликни</button>
            </div>
        </h1>
    </main>
</template>

<script>
import gsap from 'gsap';
import TextPlugin from 'gsap/TextPlugin';
import EasePack from 'gsap/EasePack';
gsap.registerPlugin(TextPlugin, EasePack.RoughEase);

const MEOWS = ['meow1', 'meow2', 'meow3', 'meow4'];

export default {
    name: 'HelloTinder',
    data() {
        return {
            words: ['Макс.', 'Фантазер.', 'Не дизайнер:('],
            // words: ['Макс.'],
            meows: [],
            counter: 0,
        }
    },
    mounted() {
        console.log('Привет, если ты залезла сюда, значит твои вкусы специфичны и вероятно совпадают с моими:)');
        MEOWS.forEach(meow => {
            this.meows.push(this.createMeowAudio(meow));
        });

        // Animations
        this.animateCursor();
        this.glowBox();
        this.mainAnimation();
    },
    methods: {
        animateCursor() {
            gsap.to('.cursor', { opacity: 0, repeat: -1, ease: 'power1.out' });
        },
        glowBox() {
            gsap.to('.box', {
                opacity: 0.8,
                ease: 'rough({strength: 3, points: 25, template: Power1.easeOut, taper: both, randomize: false, clamp: true})',
                yoyo: true,
                repeat: -1,
                duration: 2,
            });
        },
        mainAnimation() {
            const masterTl = gsap.timeline({ paused: true, repeat: 0, repeatDelay: 1 });

            const box = document.querySelector('.box');
            const hi = document.querySelector('.hi');
            const text = document.querySelector('.text');
            const clickMe = document.querySelector('.click-me');

            const boxTl = gsap.timeline();
            boxTl
                .from(box, { width: 0, ease: 'power1.out', delay: 1 })
                .from(hi, { y: '100%', opacity: '0' })
                .to(box, { height: '100%', ease: 'elastic.out(1, 0.4)', delay: 0.5 });

            masterTl.add(boxTl);

            this.words.forEach(word => {
                let wordTl = gsap.timeline({ delay: 0.5, yoyo: true, repeat: 1, repeatDelay: 1 });
                wordTl.to(text, { text: word, duration: 1 });
                masterTl.add(wordTl);
            });

            const finalWordsTl = gsap.timeline({ delay: 0.5, yoyo: false });
            finalWordsTl.to(text, { text: 'хочу чтоб ты кликнула.', duration: 1 });
            masterTl.add(finalWordsTl);

            const showButtonTl = gsap.timeline();
            showButtonTl.to(box, { backgroundColor: '#000' });
            showButtonTl.from(clickMe, { opacity: 0, backgroundColor: '#000' });

            masterTl.add(showButtonTl);

            masterTl.play();
        },
        createMeowAudio(name) {
            return new Audio(require(`./assets/meows/${name}.mp3`));
        },
        onClick() {
            if(this.counter === 3) {
                this.openTelegram();
                return;
            }
            this.playRandomMeow();
            this.counter += 1;
        },
        playRandomMeow() {
            const idx = Math.floor(Math.random() * this.meows.length);
            const meow = this.meows[idx];
            meow.play();
        },  
        openTelegram() {
            location.href = 'https://t.me/cryptodeputat'
        }, 
    },
};
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@400;700&family=Roboto:wght@400;700&display=swap');

* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

main {
    background-color: #16161a;
    width: 100vw;
    height: 100vh;
    padding: 50px;

    font-family: 'Lato', sans-serif;
    font-size: 3vw;
    color: snow;

    display: flex;
    align-items: center;
    position: relative;
}

h1 {
    position: relative;
    z-index: 2;
    overflow: hidden;
}

.hi {
    display: inline-block;
    z-index: 1;
}

.box {
    position: absolute;
    bottom: 0;
    width: 27vw;
    height: 10%;
    background-color: #7f5af0;
    z-index: -1;
}

.content {
    display: grid;
    grid-template-columns: max-content 1fr;
    grid-template-areas: 
        'hello animated-text'
        'click-me click-me';
    gap: 16px;
}

.hello {
    grid-area: hello;
    height: 7.5vw;
    position: relative;
}

.animated-text {
    grid-area: animated-text;
    height: 100%;
    min-height: 15vw;
}

.click-me {
    grid-area: click-me;
    justify-self: center;
    align-self: center;
}

.click-me button {
    all: unset;
    padding: 8px 12px;
    background: #7f5af0;
    font-size: 3vw;
    cursor: pointer;
    transition: all .15s ease-in;
}

.click-me button:hover {
    opacity: .8;
}

.click-me button:focus {
    opacity: 1;
}
</style>
