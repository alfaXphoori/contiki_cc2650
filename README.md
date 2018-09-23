# contiki_cc2650
Learning contiki-os with Ubuntu 18.04 on cc2650 

* [Install Toolchain](#install-toolchain)
* [Flash program by Ubuntu](#flash-program-by-ubuntu)

## Install Toolchain
* Download and install Ubuntu จาก www.ubuntu.com
  ![tools-1](https://user-images.githubusercontent.com/27261111/45924664-e4ee7100-bf2f-11e8-9c7c-18328c487c6b.png)
* เมื่อ install เสร็จทำการ update ubuntu และ install git ผ่านทาง terminal:
  ```
  sudo apt-get upgade -y
  sudo apt-get install git -y
  sudo apt-get install build-essential -y

  ```
* Install gcc-arm embedded and other Toolchain ด้วยคำสั่ง
  ```
  sudo add-apt-repository ppa:team-gcc-arm-embedded/ppa
  sudo apt-get update -y
  sudo apt-get install gcc-arm-embedded -y
  sudo apt-get install srecord -y
  sudo apt-get install python-serial
  ```
  เมื่อทำการติดตั้งเสร็จจะได้ และลองตรวจสอบ arm-gcc ด้วยคำสั่ง จะได้ดังภาพ
  ```
  arm-none-eabi-gcc --version
  ```
  ![tools-2](https://user-images.githubusercontent.com/27261111/45929330-43404180-bf7a-11e8-8fcc-416dc660c07a.png)
* ทำการ install Contiki-os ด้วยการ Clone Contiki-os จาก github เมื่อ clone เสร็จแล้วทำการ update submodule ของ Contiki-os
  ```
  git clone https://github.com/contiki-os/contiki
  cd contiki
  git submodule update –init
  ```
  ![tools-3](https://user-images.githubusercontent.com/27261111/45929364-c5306a80-bf7a-11e8-8cf2-4b2fc9ed644c.png)

## Flash program by Ubuntu
* ทำการ Config file ccfg.c ใน Contiki โดยใช้ editor ต่างๆ โดยผู้เขียนจะใช้ vim ในการ edit
  ```
  vim contiki/cpu/cc26xx-cc13xx/lib/cc26xxware/startup_files/ccfg.c
  ```
  Change as:
  ```
  #define SET_CCFG_BL_CONFIG_BOOTLOADER_ENABLE  0xC5
  #define SET_CCFG_BL_CONFIG_BL_LEVEL           0x0
  #define SET_CCFG_BL_CONFIG_BL_PIN_NUMBER      0x0D
  #define SET_CCFG_BL_CONFIG_BL_ENABLE          0xC5
  ```
* เมื่อเสร็จแล้วทำการ compile program ใน folder contiki/example/cc26xx โดยใช้คำสั่ง
  ```
  cd contiki/example/cc26xx
  make BOARD=launchpad/cc2650 cc26xx-demo
  ```
  ![tools-4](https://user-images.githubusercontent.com/27261111/45929470-86031900-bf7c-11e8-8abd-8a4fdba02a48.png)
* เมื่อทำการ compile เสร็จสิ้นจะได้ ไฟล์ cc26xx-demo.hew มา นำไป flash program ครั้งแรกเพื่อทำติดตั้ง bootloder ใหม่ โดยโปรแกรม Flash programmer 2 หรือ Uniflash
  * [FLASH-PROGRAMMER-2](http://www.ti.com/tool/FLASH-PROGRAMMER)
  * [UNIFLASH](http://www.ti.com/tool/UNIFLASH)
* โดยการ Flash program เราจะต้องมีการ config ค่า baudrate ในค่าที่บอร์ดรองรับในทีนี้เป็น 115200 โดยทำตามขั้นตอน
  ```
  vim contiki/platform/srf06-cc26xx/launchpad/Makefile.launchpad
  ```
  add:
  ```
  BSL_FLAGS += -e -w -v -b 115200
  ```
  ![tools-11](https://user-images.githubusercontent.com/27261111/45929592-1d1ca080-bf7e-11e8-8dc0-2950e194b05f.png)
* โดยการ Flash program จะต้องมาการเข้าสู้ bootloader ด้วยการกดปุ่ม BTN-1 + RESET บนบอร์ด และการทำ flash ตามคำสั่ง
  ```
  cd contiki/example/cc26xx
  make BOARD=launchpad/cc2650 cc26xx-demo.upload PORT=/dev/ttyACM0
  ```
  ถ้า Error ตามภาพ ในทุกครั้งที่มีการ flash ผ่าน ubuntu จะต้องมีการกด ปุ่ม BTN-1 + RESET ทุกครั้ง ถ้าไม่กดแสดงว่าเข้าbootloader ไม่ได้มันจึง error
  ![tools-12](https://user-images.githubusercontent.com/27261111/45929628-99af7f00-bf7e-11e8-8cc7-d1d6c536f534.png)
  สุดท้ายถ้าทำตามขั้นตอนทั้งหมด และถูกต้องเราก็จะสามารถ flash program ผ่าน ubuntu ได้ดังรูป
  ![tools-13](https://user-images.githubusercontent.com/27261111/45929638-c368a600-bf7e-11e8-88b7-8fb680412d93.png)
