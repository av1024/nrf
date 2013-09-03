Custom implementation of SPI and nRF24L01+.

Usage:
 - chekcout as sub-module.
 - copy nrf.h from repo to project (upper) folder
 - change pin defines in copied nrf.h
 
```
// setup
uint8_t nrf_rx0_addr[ADDRESS_WIDTH] = {0xe7, 0xe7, 0xe7, 0xe7, 0xe7};
uint8_t nrf_tx_addr[ADDRESS_WIDTH] = {0xe7, 0xe7, 0xe7, 0xe7, 0xe7};
uint8_t nrf_rx1_addr[ADDRESS_WIDTH] = {0xc2, 0xc2, 0xc2, 0xc2, 0xc2};
uint8_t nrf_cfg[] = {
            (1<<ENAA_P0) | (1<<ENAA_P1),
            (1<<ERX_P0) | (1<<ERX_P1),
            AW_5, // 5 bytes address
            ARC_5 | ARD_1000, // 1000ms delay, 5 retransmits
            RF_CHANNEL,
            RF_1MBPS | RF_0DBM
        };

nrf_init();
nrf_rx_config(nrf_cfg,
              nrf_rx0_addr,
              nrf_rx1_addr,
              nrf_tx_addr);
    
// usage for rx:
RX_POWERUP;
CE_HIGH;
_delay_us(150);
// ...
if (nrf_data_available()) {
        uint8_t size = nrf_read(R_RX_PL_WID);
        if (size > 32) {
            nrf_command(FLUSH_RX);
            nrf_config_register(STATUS, (1<<RX_DR));
        } else {
            uint8_t pipe = nrf_get_payload(buf, size); // pipe + 1
            nrf_config_register(STATUS, (1<<RX_DR));
        }
}
    
// usage for tx:
uint8_t buf[32] = { 1,2,3,4,5 };
if (uart_send(buf, 5)) {
    return 1
}
```
    
 
Defaults:
 - 5-byte address
 - dynamic payload
 - IRQ pin connected to INT1, INT1 should be enabled
 
