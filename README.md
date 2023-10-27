var song;
var fft;
var particles = [];
var img;
var g = 0;
var b = 0;
var cycleDuration = 20000; // 


function preload() {
    inputbtn = createFileInput(()=>{
    //     song = loadSound(); 
    }); 
    song = loadSound("sample-visualisation (1).mp3");
    img = loadImage("WechatIMG1453.jpg");
}

function setup() {
    createCanvas(windowWidth, windowHeight);
    angleMode(DEGREES);
    imageMode(CENTER);
    rectMode(CENTER);
    fft = new p5.FFT(0.8, 512);
    // img.filter(BLUR, 1);
    loop();
}

function draw() {
    background(0);
    var spectrum = fft.analyze();
    translate(width / 2, height / 2);
    // rotate(frameCount * 0.01);
    
    image(img,0,0,width+100,height+100);
    
    var time = millis() % cycleDuration; // 
    if (time < cycleDuration / 2) {
        g = map(time, 0, cycleDuration / 2, 0, 255); // 从0增加到255
        b = map(time, cycleDuration / 2, cycleDuration, 255, 0);
    } else {
        g = map(time, cycleDuration / 2, cycleDuration, 255, 0); // 从255逐渐变到0
        b = map(time, 0, cycleDuration / 2, 0, 255); 
    }
    for (var i = 0; i < spectrum.length; i++) {
        var angle = map(i, 0, spectrum.length, 0, 360);
        var amp = spectrum[i];
        var r = map(amp, 0, 256, 150, height / 2);
        var x = r * cos(angle);
        var y = r * sin(angle);
        var col = color(map(amp, 0, 256, 0, 255), g, b);
        particles[i] = new Particle(x, y, col);
        particles[i].show();
    }
}

function mouseClicked() {
    if(song.isPlaying()) {
        song.pause();
        noLoop();
    } else {
        song.play();
        loop();
    }
}

class Particle {
    constructor(x, y, col) {
        this.x = x;
        this.y = y;
        this.col = col;
    }
    show() {
        push();
        translate(this.x, this.y);
        rotate(frameCount);
        stroke(this.col);
        line(0, 0, 15, 15);
        pop();
    }
}
