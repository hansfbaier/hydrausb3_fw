## HydraUSB3_DualBoard_SerDes
HydraUSB3_DualBoard_SerDes repository contains open source (see [LICENSE](../LICENSE)) test firmware for HydraUSB3 v1 board using WCH CH569W MCU.
* Contributor shall check [CODING_STYLE.md](../CODING_STYLE.md)
* For more details on HydraUSB3 v1 see https://hydrabus.com/hydrausb3-v1-0-specifications

This example(DualBoard) requires 2x HydraUSB3 v1 boards to be plugged together.
* First HydraUSB3 board(on bottom) shall have PB24 not populated (called RX mode)
* Seconds HydraUSB3 board(on top of First board) shall have PB24 populated with a 2.54mm Jumper (called TX mode)

The aim of this example is to use SerDes with Dual HydraUSB3 v1 boards connected together with following features/API
* Main code available in [User/Main.c](User/Main.c)
* Log over serial port (5000000 bauds 8N1 in that example which requires USB2HS Serial like FTDI C232HM-DDHSL-0) with timestamp(second, millisecond, microsecond).
    | FTDI C232HM-DDHSL-0 Pin | HydraUSB3 Pin   |
    |-------------------------|-----------------|
    | Orange ADBUS0 TX(Pin2)  | J5 RXD1         |
    | Yellow ADBUS1 RX(Pin3)  | J5 TXD1         |
    | Black GND (Pin10)       | GND (near RXD1) |
  * For more details on HydraUSB3 v1 WCH CH569 UART see https://github.com/hydrausb3/hydrausb3_hw/blob/main/HydraUSB3_V1_CH569_UART.ods
* Usage of PB24 GPIO to detect TX mode/RX mode board (with API `hydrausb3_pb24()`)
* Synchronize 2 HydraUSB3 v1 boards at startup using 2 GPIOs (with API `hydrausb3_sync2boards()`).
* Usage of SerDes in TX mode (with API `SerDes_Tx_Init()`, `SerDes_DMA_Tx_CFG()` and `SerDes_DMA_Tx() / SerDes_Wait_Txdone()`)
* Usage of SerDes in RX mode (with API `SerDes_DoubleDMA_Rx_CFG()`)
* Usage of SerDes Interrupt see (`SERDES_IRQHandler()` for Receive code)

This example is a very basic example to send different data/size(each 2s) over SerDes from one board to an other board
* When pressing continuously **UBTN** 4K are sent in loop on SerDes each 100us.

Example output on Serial Port on RXD1:
```
00s 000ms 020us SYNC 00000001
00s 000ms 000us Start
00s 000ms 076us SerDes_Rx 2022/07/30 start @ChipID=69 (CPU Freq=120 MHz)
00s 000ms 288us SerDes_DoubleDMA_Rx_CFG() Before
00s 000ms 429us RX_DMA0_addr=0x20022000 RX_DMA1_addr=0x20021000
00s 000ms 627us SerDes_DoubleDMA_Rx_CFG() After
00s 000ms 766us SerDes_Rx_Init(SERDES_TX_RX_SPEED=0x1200) Before
00s 001ms 230us SerDes_Rx_Init() After
00s 001ms 347us SerDes_EnableIT(SDS_RX_INT_EN|SDS_RX_ERR_EN|SDS_FIFO_OV_EN) Before
00s 001ms 571us SerDes_EnableIT(SDS_RX_INT_EN|SDS_RX_ERR_EN|SDS_FIFO_OV_EN) After
02s 001ms 222us SDS_RX_LEN0=8 SDS_RX_LEN1=8 CNT_nb_cycles=113(0us)
02s 001ms 437us SDS_STATUS[0]=0x010D0004 SDS_STATUS[1]=0x020D0004 SDS_DATA0=0x2FFFFFFF SDS_DATA1=0x3FFFFFFF
02s 001ms 765us SDS_RX_ERR=0 SDS_FIFO_OV=0 RX_CRC_OK=1
02s 001ms 942us AAAA5555 AAAA5555
02s 002ms 066us AAAA5555 AAAA5555
04s 001ms 235us SDS_RX_LEN0=16 SDS_RX_LEN1=16 CNT_nb_cycles=130(1us)
04s 001ms 455us SDS_STATUS[0]=0x030D0004 SDS_STATUS[1]=0x040D0004 SDS_DATA0=0x4FFFFFFF SDS_DATA1=0x5FFFFFFF
04s 001ms 782us SDS_RX_ERR=0 SDS_FIFO_OV=0 RX_CRC_OK=1
04s 001ms 960us 11111111 11111111 11111111 11111111
04s 002ms 149us 11111111 11111111 11111111 11111111
06s 001ms 248us SDS_RX_LEN0=64 SDS_RX_LEN1=64 CNT_nb_cycles=172(1us)
06s 001ms 468us SDS_STATUS[0]=0x050D0004 SDS_STATUS[1]=0x060D0004 SDS_DATA0=0x6FFFFFFF SDS_DATA1=0x7FFFFFFF
06s 001ms 796us SDS_RX_ERR=0 SDS_FIFO_OV=0 RX_CRC_OK=1
06s 001ms 974us 22222222 22222222 22222222 22222222 22222222 22222222 22222222 22222222 22222222 22222222 22222222 22222222 22222222 22222222 22222222 22222222
06s 002ms 551us 22222222 22222222 22222222 22222222 22222222 22222222 22222222 22222222 22222222 22222222 22222222 22222222 22222222 22222222 22222222 22222222
...
```

Example output on Serial Port on TXD1:
```
00s 000ms 020us SYNC 00000001
00s 000ms 000us Start
00s 000ms 076us SerDes_Tx 2022/07/30 start @ChipID=69 (CPU Freq=120 MHz)
00s 000ms 288us TX_DMA_addr=0x20020000
00s 000ms 415us SerDes_Tx_Init(SERDES_TX_RX_SPEED=0x1200) Before
00s 000ms 876us SerDes_Tx_Init() After
00s 001ms 022us Wait 100us
```

For more details on how to build and flash this example on HydraUSB3 v1 board see the Wiki:
* For GNU/Linux:
  * https://github.com/hydrausb3/hydrausb3_fw/wiki/how-to-build-flash-and-use-examples-on-linux
* For Windows:
  * https://github.com/hydrausb3/hydrausb3_fw/wiki/how-to-build-flash-and-use-examples-on-windows
