I2C1.setup({ scl: B8, sda: B9 });

var analogValueFS37;
var dht = require("DHT11").connect(C11);
var bme = require("BME280").connect(I2C1);
var lcd = require("HD44780").connect(A0, A1, C0, C1, C2, C3);

var fs37LRain = 0.7;
var fs37MRain = 0.4;
var fs37HRain = 0.2;

var data; // Zmienna globalna do przechowywania danych z BME280
var temp_act; // Zmienna globalna
var press_act; // Zmienna globalna

var  on = false;

LED1.write(on);
LED2.write(on);
LED3.write(on);

function readFS37(callback) {
  analogValueFS37 = analogRead(C5);
  callback(analogValueFS37);
}

function readDHT11(callback) {
  dht.read(function (dhtData) {
    callback(dhtData);
  });
}

function readBME280(callback) {
  data = bme.readRawData();
  var temp_cal = bme.calibration_T(bme.temp_raw);
  var press_cal = bme.calibration_P(bme.pres_raw);
  temp_act = temp_cal / 100.0;
  press_act = press_cal / 100.0;
  callback({ temp: temp_act, pr: press_act });
}

function showReadingsOnLCD() {
  readBME280(function (bmeData) {
    lcd.clear();
    if (bmeData && bmeData.temp > 30.0) {
      lcd.print(Math.floor(bmeData.temp) + " *Hot*");
    } else if (bmeData && bmeData.temp < 0.0) {
      lcd.print(Math.floor(bmeData.temp) + " *Cold*");
    } else {
      lcd.setCursor(0, 0);
      lcd.print(Math.floor(bmeData.temp) + "C");
    }

    readDHT11(function (dhtData) {
      lcd.setCursor(4, 0);
      if (dhtData && dhtData.rh > 70.0) {
        lcd.print(dhtData.rh + "%");
      } else {
        lcd.print(dhtData.rh + "%");
      }

      readBME280(function (bmeData) {
        lcd.setCursor(0, 1);
        if (bmeData && bmeData.pr < 1013.25) {
          lcd.print(Math.floor(bmeData.pr) + "hPa");
        } else if (bmeData && bmeData.pr > 1013.25) {
          lcd.print(Math.floor(bmeData.pr) + "hPa");
        } else {
          lcd.print(Math.floor(bmeData.pr) + "hPa");
        }

        readFS37(function (analogValueFS37) {
          if (analogValueFS37 > fs37LRain) {
            LED1.write(0);
            LED2.write(1);
            LED3.write(0);
          } else if (analogValueFS37 > fs37MRain) {
            LED1.write(0);
            LED2.write(0);
            LED3.write(1);
          } else if (analogValueFS37 > fs37HRain) {
            LED1.write(1);
            LED1.write(0);
            LED2.write(0);
          }
        });
      });
    });
  });
}

function fs37Show() {
  readFS37(function (analogValueFS37) {
    print("FS-37: " + analogValueFS37 + "V");
  });
}

setInterval(fs37Show, 1000);
setInterval(showReadingsOnLCD, 1000);

