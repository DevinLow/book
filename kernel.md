#### add pwm2  
#### pwm2 con't setup in sys_config.fex, all resouce need add to dts,if setup in sys_config.fex ,there is a error 
##### 1.add pwm resouce in A40I-SDK/lichee/linux-3.10/arch/arm/boot/dts/sun8iw11p1.dtsi  
```c
pwm: pwm@01c23400 {
	compatible = "allwinner,sunxi-pwm";
	reg = <0x0 0x01c23400 0x0 0x154>;
	//pwm-number = <1>;
	pwm-number = <3>; //number of pwms
	pwm-base = <0x0>;
	pwms = <&pwm0>, <&pwm1>, <&pwm2>;
};
pwm2: pwm2@01c23400 {
	compatible = "allwinner,sunxi-pwm0";
	pinctrl-names = "active", "sleep";
	reg_base = <0x01c23400>;
	reg_peci_offset = <0x00>;
	reg_peci_shift = <0x02>;
	reg_peci_width = <0x01>;
	
	reg_pis_offset = <0x04>;
	reg_pis_shift = <0x02>;
	reg_pis_width = <0x01>;
	
	reg_crie_offset = <0x10>;
	reg_crie_shift = <0x04>;
	reg_crie_width = <0x01>;
	
	reg_cfie_offset = <0x10>;
	reg_cfie_shift = <0x05>;
	reg_cfie_width = <0x01>;
	
	reg_cris_offset = <0x14>;
	reg_cris_shift = <0x04>;
	reg_cris_width = <0x01>;
	
	reg_cfis_offset = <0x14>;
	reg_cfis_shift = <0x05>;
	reg_cfis_width = <0x01>;
	
	reg_clk_src_offset = <0x24>;
	reg_clk_src_shift = <0x07>;
	reg_clk_src_width = <0x02>;
	
	reg_bypass_offset = <0x24>;
	reg_bypass_shift = <0x05>;
	reg_bypass_width = <0x01>;
	
	reg_clk_gating_offset = <0x24>;
	reg_clk_gating_shift = <0x04>;
	reg_clk_gating_width = <0x01>;
	
	reg_clk_div_m_offset = <0x24>;
	reg_clk_div_m_shift = <0x00>;
	reg_clk_div_m_width = <0x04>;
	
	reg_pdzintv_offset = <0x34>;
	reg_pdzintv_shift = <0x08>;
	reg_pdzintv_width = <0x08>;
	
	reg_dz_en_offset = <0x34>;
	reg_dz_en_shift = <0x00>;
	reg_dz_en_width = <0x01>;
	
	reg_enable_offset = <0x40>;
	reg_enable_shift = <0x02>;
	reg_enable_width = <0x01>;
	
	reg_cap_en_offset = <0x44>;
	reg_cap_en_shift = <0x02>;
	reg_cap_en_width = <0x01>;
	
	reg_period_rdy_offset = <0xA0>;
	reg_period_rdy_shift = <0x0b>;
	reg_period_rdy_width = <0x01>;
	
	reg_pul_start_offset = <0xA0>;
	reg_pul_start_shift = <0x0a>;
	reg_pul_start_width = <0x01>;
	
	reg_mode_offset = <0xA0>;
	reg_mode_shift = <0x09>;
	reg_mode_width = <0x01>;
	
	reg_act_sta_offset = <0xA0>;
	reg_act_sta_shift = <0x08>;
	reg_act_sta_width = <0x01>;
	
	reg_prescal_offset = <0xA0>;
	reg_prescal_shift = <0x00>;
	reg_prescal_width = <0x08>;
	
	reg_entire_offset = <0xA4>;
	reg_entire_shift = <0x10>;
	reg_entire_width = <0x10>;
	
	reg_active_offset = <0xA4>;
	reg_active_shift = <0x00>;
	reg_active_width = <0x10>;
	device_type = "pwm2";
	pwm_used = <0x1>;
	pinctrl-0 = <&pwm2_pins_a>;
	pinctrl-1 = <&pwm2_pins_b>;
};
```
#### 2.add pinctrl resouce A40I-SDK/lichee/linux-3.10/arch/arm/boot/dts/sun8iw11p1-pinctrl.dtsi  
```
pwm2_pins_a: pwm2@0 {
	allwinner,pins = "PI20";
	allwinner,function = "pwm2";
	allwinner,pname = "pwm_positive";
	allwinner,muxsel = <6>;
	allwinner,drive = <0xffffffff>;
	allwinner,data = <0xffffffff>;
	allwinner,pull = <0>;
};

pwm2_pins_b: pwm2@1 {
	allwinner,pins = "PI20";
	allwinner,function = "io_disabled";
	allwinner,pname = "pwm_positive";
	allwinner,muxsel = <7>;
	allwinner,drive = <0xffffffff>;
	allwinner,data = <0xffffffff>;
	allwinner,pull = <0>;
};

```