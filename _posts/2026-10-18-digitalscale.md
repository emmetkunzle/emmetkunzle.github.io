---
layout: post
title: "Digital Scale Project"
date: 2026-10-18 12:00:00 -0400
category: project
image: /assets/digitalscale_images/IMG_5385.JPG
excerpt_separator: "<!--more-->"
---

Goal: Build a digital scale that can measure mass in both grams and ounces up to at least 5kg

<!--more-->

!["Scale Off Image1.1.0"](/assets/digitalscale_images/IMG_5385.JPG){: width="399"}
!["Big img test"](/assets/digitalscale_images/IMG_5378.JPG){: width="399"}


### Arduino Program:
Running on Arduino Nano (Old Bootloader)
```
#include <U8g2lib.h>
#include "Adafruit_HX711.h"

const uint8_t DATA_PIN = 5;
const uint8_t CLOCK_PIN = 6;`

Adafruit_HX711 hx711(DATA_PIN, CLOCK_PIN);

U8G2_SSD1327_VISIONOX_128X96_1_4W_HW_SPI u8g2(
  U8G2_R0,
  /* CS=*/ 10,
  /* DC=*/ 9,
  /* RESET=*/ 8
);

char avgWeightString[8];
double unitConv = 0;
int precision = 0;
bool units = 0;

void setup() {
  pinMode(4, INPUT);
  pinMode(7, INPUT);

  hx711.begin();
  u8g2.begin();

  for (uint8_t t=0; t<5; t++) {
      hx711.tareA(hx711.readChannelRaw(CHAN_A_GAIN_128));
      delay(50);
      hx711.tareA(hx711.readChannelRaw(CHAN_A_GAIN_128));
  }
}

void loop() {

  if(digitalRead(7) == HIGH) {
    for (uint8_t t=0; t<5; t++) {
      hx711.tareA(hx711.readChannelRaw(CHAN_A_GAIN_128));
      hx711.tareA(hx711.readChannelRaw(CHAN_A_GAIN_128));
    }
  }

  if(digitalRead(4) == LOW) {
    //grams
    unitConv = 0.001132867133;
    units = 0;
  } else if(digitalRead(4) == HIGH) {
    //oz
    unitConv = 0.000039960039964726634;
    units = 1;
  }

  double weight = (double)(hx711.readChannelBlocking(CHAN_A_GAIN_128)) * unitConv;
  float avgWeight = 0.0;

  float sum = 0;
  int sampleRateDisplay= 80;

  for(int i = 0; i < sampleRateDisplay; i++) {
    sum += (hx711.readChannelBlocking(CHAN_A_GAIN_128) * unitConv);
  }
  avgWeight = sum / sampleRateDisplay;

  if(avgWeight < 0) {
    precision = 2;
  }  else if(avgWeight < 10) {
    precision = 3;
  } else if(avgWeight < 100) {
    precision = 2;
  } else {
    precision = 1;
  }

  dtostrf(avgWeight, -5, precision, avgWeightString);

  u8g2.firstPage();
  do {
    drawScreen_1();
  } while (u8g2.nextPage());

  delay(50);
}



void drawScreen_1(void) {
    u8g2.setFontMode(1);
    u8g2.setBitmapMode(1);
    
    u8g2.setFont(u8g2_font_profont12_tr);
    u8g2.drawStr(21, 30, "Weight:");
    u8g2.drawBox(21, 32, 84, 24);
    u8g2.drawBox(40, 63, 48, 14);

    u8g2.setDrawColor(2);
    u8g2.setFont(u8g2_font_profont29_tr);
    u8g2.drawStr(24, 54, avgWeightString);

    u8g2.setFont(u8g2_font_profont12_tr);
    u8g2.drawStr(49, 72, "g");
    u8g2.drawStr(70, 72, "oz");
    
    if(units == 1) {
      u8g2.drawBox(41, 64, 23, 12);
    } else {
      u8g2.drawBox(64, 64, 23, 12);
    }
}
```