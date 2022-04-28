<script>
  import firebase from "firebase/app";
  import { Mic, MicOff, CallEnd } from "@material-ui/icons";
  import "firebase/database";
  import "firebase/analytics";
  import Peer from "peerjs";
  import Tailwindcss from "./Tailwindcss.svelte";

  var firebaseConfig = {
    apiKey: "AIzaSyCPWeze8Os5oTt2rX_tpSjwiD9vh51RbZ8",
    authDomain: "clubhouse-timed-working-title.firebaseapp.com",
    databaseURL:
      "https://clubhouse-timed-working-title-default-rtdb.europe-west1.firebasedatabase.app",
    projectId: "clubhouse-timed-working-title",
    storageBucket: "clubhouse-timed-working-title.appspot.com",
    messagingSenderId: "451960023573",
    appId: "1:451960023573:web:27569a7eca886f0962fc1c",
    measurementId: "G-L180K9GSW1",
  };

  window.connected = false;

  firebase.initializeApp(firebaseConfig);
  firebase.analytics();

  const database = firebase.database().ref().child("Matchmaking");
  //.on("value", (snapshot) => console.log(snapshot.val()));
  window.peer = new Peer();
  const profilePic = document.location.href
    .match(/profile_pic=[^?]*/)[0]
    .slice(12);
  window.peer.on("open", (id) => {
    console.log(id);
    const payload = {
      Snapchat:
        document.location.href.match(/Snapchat=[^&]*/) != null
          ? document.location.href.match(/Snapchat=[^&]*/)[0].slice(9)
          : true,

      Instagram:
        document.location.href.match(/Instagram=[^&]*/) != null
          ? document.location.href.match(/Instagram=[^&]*/)[0].slice(11)
          : true,
      Twitter:
        document.location.href.match(/Twitter=[^&]*/) != null
          ? document.location.href.match(/Twitter=[^&]*/)[0].slice(10)
          : true,
    };
    console.log(payload);
    database
      .child(window.peer.id)
      .set({ socials: payload, profilePic: profilePic });
  });
  const cancel = () => {
    database.child(window.peer.id).remove();
  };
  window.onbeforeunload = cancel();
  window.peer.on("error", function (err) {
    console.log("Error: ", err);
  });
  navigator.mediaDevices // get microphone
    .getUserMedia({ audio: true })
    .then((stream) => {
      window.localStream = stream;
    })
    .catch((err) => {
      console.log("u got an error:" + err);
    });
</script>

<Tailwindcss />
<main>
  <h3>Matchmaking in progress...</h3>
  <button id="cancelBtn" on:click={cancel()}>Cancel</button>
  <!--<audio id="remoteAudio" autoPlay />-->
</main>
