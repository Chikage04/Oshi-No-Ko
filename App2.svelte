<script>
  import { onMount } from "svelte";
  import { initializeApp } from "firebase/app";
  import { getDatabase, ref, set, onValue } from "firebase/database";

  let img = [
    { src: "https://pbs.twimg.com/media/FlyK1nFaYAA1hJC.jpg" },
    { src: "https://m.media-amazon.com/images/I/71bgnycGk6L.jpg" },
    {
      src: "https://www.manga-news.com/public/images/vols/Oshi-no-Ko-11-jp.jpg",
    },
    {
      src: "https://www.manga-news.com/public/images/dvd/oshi-no-ko-anime-visual-2.jpg",
    },
    {
      src: "https://preview.redd.it/fcy3hcdm6y281.jpg?auto=webp&s=b00dcfd6cbc8dcf4c8bc531e1cc7a336f9beca18",
    },
  ];

  const firebaseConfig = {
    apiKey: "AIzaSyCSUQoYmYbmbkKoIbDQcQhOxnada13rBWY",
    authDomain: "memory-c1192.firebaseapp.com",
    projectId: "memory-c1192",
    storageBucket: "memory-c1192.appspot.com",
    messagingSenderId: "320978503728",
    appId: "1:320978503728:web:49c1ef1f72cd9a595745f5",
    measurementId: "G-QP4M89SW8F",
    databaseURL:
      "https://memory-c1192-default-rtdb.europe-west1.firebasedatabase.app/",
  };
  const firebaseApp = initializeApp(firebaseConfig);
  const db = getDatabase(firebaseApp);

  let data = Array(img.length).fill(0);

  img = img.map((image, index) => {
    return {
      ...image,
      bgColor: getRandomColor(),
      index: index,
    };
  });

  function getRandomColor() {
    return `rgb(${Math.floor(Math.random() * 256)}, ${Math.floor(
      Math.random() * 256
    )}, ${Math.floor(Math.random() * 256)}, 0.6)`;
  }
  let top = true;
  let timeoutOn = true;
  let top2 = true;
  let timeoutOn2 = true;
  let truc = true;

  function next(index) {
    if (truc == false) return;
    truc = false;
    let first = data[index];
    setTimeout(() => {
      onValue(ref(db, "images"), (snapshot) => {
        const dataFromFirebase = snapshot.val();
        if (dataFromFirebase) {
          data = Object.values(dataFromFirebase);
        }
      });
      if((first - data[index] == 120 || data[index] - first == -120 )){
        data[index] = first;
      }
    }, 120000);
  }
  function sendData(index) {
    if (top === false) return;
    top = false;
    const oldValue = data[index];
    set(ref(db, `images/${index}`), data[index] + 1);
    /*if(timeoutOn === true){
        timeoutOn = false;
        setTimeout(() => {
        timeoutOn = true;
        onValue(ref(db, `images/${index}`), (snapshot) => {
          const newValue = snapshot.val();
          if (newValue - oldValue > 200) {
            console.log('test2');
            set(ref(db, `images/${index}`), oldValue);
          }
          console.log('test');
        });
      }, 3000);*/

    setTimeout(() => {
      top = true;
    }, 100);
  }
  function sendData2(index) {
    if (top2 === false) return;
    top2 = false;
    const oldValue = data[index];
    set(ref(db, `images/${index}`), data[index] - 1);
    /*if(timeoutOn2 === true){
        timeoutOn2 = false;
        setTimeout(() => {
        timeoutOn2 = true;
        onValue(ref(db, `images/${index}`), (snapshot) => {
          const newValue = snapshot.val();
          if (oldValue - newValue  > 200) {
            console.log('test2');
            set(ref(db, `images/${index}`), oldValue);
          }
          console.log('test');
        });
      }, 3000);
      }*/
    setTimeout(() => {
      top2 = true;
    }, 100);
  }

  onMount(() => {
    // Récupérer les données initiales
    onValue(ref(db, "images"), (snapshot) => {
      const dataFromFirebase = snapshot.val();
      if (dataFromFirebase) {
        data = Object.values(dataFromFirebase);
      }
    });
  });
  function scrollToBottom() {
    window.scrollTo({
      top: document.body.scrollHeight,
      behavior: "smooth",
    });
  }
</script>

<main>
  <a href="#" on:click={scrollToBottom}>
    <img
      id="bs"
      src="https://www.animeunited.com.br/oomtumtu/2023/03/portada_oshi-no-ko-17.jpg"
      alt="scantrad"
    />
    <p id="mac">Bas de page</p>
  </a>
  {#each img as image}
    <div
      class="box"
      id={`box-${image.index}`}
      style={`background-color: ${image.bgColor}`}
    >
      <div class="buttons">
        <button
          on:click={() => {
            sendData(image.index), next(image.index);
          }}>Yes</button
        >
        <button
          on:click={() => {
            sendData2(image.index), next(image.index);
          }}>No</button
        >
      </div>
      <img src={image.src} alt="jsp" />
      <span>{`Score = ${data[image.index]}`}</span>
    </div>
  {/each}
  <a id="bas">
    <div />
  </a>
</main>

<style>
  * {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
    overflow: hidden;
  }

  .box {
    height: 100vh;
    clip-path: polygon(0 0, 100% 0, 100% 100%, 0% 100%);
    position: relative;
  }

  img {
    object-fit: cover;
    height: 80%;
    width: 30%;
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
  }
  a {
    z-index: 9999;
  }
  #bs {
    z-index: 9999;
    object-fit: cover;
    height: 52px;
    width: 50px;
    position: fixed;
    top: 6%;
    left: 3%;
    border-radius: 29%;
    opacity: 0.9;
  }
  @import url("https://fonts.googleapis.com/css2?family=Poppins:wght@500&display=swap");

  #mac {
    z-index: 9999;
    width: 50px;
    position: fixed;
    top: 9%;
    left: 1%;
    font-size: 15px;
    color: #fff;
    margin-left: 5px;
    margin-top: 5px;
    opacity: 0.9;
    text-align: center;
    font-family: Poppins;
  }

  .buttons {
    background-color: #00000050;
    display: flex;
    position: relative;
    top: 43%;
    left: 5%;
    width: 380px;
    gap: 10px;
    --b: 5px; /* the border thickness */
    --h: 1.8em; /* the height */
  }

  .buttons button {
    --_c: #88c100;
    flex: calc(1.25 + var(--_s, 0));
    min-width: 0;
    font-size: 40px;
    font-weight: bold;
    height: var(--h);
    cursor: pointer;
    color: var(--_c);
    border: var(--b) solid var(--_c);
    background: conic-gradient(
        at calc(100% - 1.3 * var(--b)) 0,
        var(--_c) 209deg,
        #0000 211deg
      )
      border-box;
    clip-path: polygon(0 0, 100% 0, calc(100% - 0.577 * var(--h)) 100%, 0 100%);
    padding: 0 calc(0.288 * var(--h)) 0 0;
    margin: 0 calc(-0.288 * var(--h)) 0 0;
    box-sizing: border-box;
    transition: flex 0.4s;
  }
  .buttons button + button {
    --_c: #ff003c;
    flex: calc(0.75 + var(--_s, 0));
    background: conic-gradient(
        from -90deg at calc(1.3 * var(--b)) 100%,
        var(--_c) 119deg,
        #0000 121deg
      )
      border-box;
    clip-path: polygon(calc(0.577 * var(--h)) 0, 100% 0, 100% 100%, 0 100%);
    margin: 0 0 0 calc(-0.288 * var(--h));
    padding: 0 0 0 calc(0.288 * var(--h));
  }
  .buttons button:focus-visible {
    outline-offset: calc(-2 * var(--b));
    outline: calc(var(--b) / 2) solid #000;
    background: none;
    clip-path: none;
    margin: 0;
    padding: 0;
  }
  .buttons button:focus-visible + button {
    background: none;
    clip-path: none;
    margin: 0;
    padding: 0;
  }
  .buttons button:has(+ button:focus-visible) {
    background: none;
    clip-path: none;
    margin: 0;
    padding: 0;
  }
  button:hover,
  button:active:not(:focus-visible) {
    --_s: 0.75;
  }
  button:active {
    box-shadow: inset 0 0 0 100vmax var(--_c);
    color: #fff;
  }
  button:active {
    box-shadow: inset 0 0 0 100vmax var(--_c);
    color: #8a0909;
  }

  span {
    position: relative;
    top: 50%;
    left: 11%;
    color: white;
    z-index: 2;
    font-size: 33px;
    font-weight: 500;
  }
  @media screen and (max-width: 1090px) {
    .buttons {
      left: 1px;
    }
    span {
      left: 1%;
      color: #ffffff;
      z-index: 999;
      font-weight: 600;
    }
  }
</style>
