###  测试发现power按下的时候，关闭LED流程存在问题，背光与屏的VCC都未关闭，分析原理发现，公版设计的VCC电源由PMU控制，而我们的用GPIO控制，驱动中无相关流程。
#### 1.背光开关处理 disp_sys_gpio_set_value(lcdp->lcd_cfg.lcd_bl_gpio_hdl, 1, "lcd_bl_off"); //add by Devin 原理图设计是低开，高关。
#### 代码上处理方式，发现开启的时候，disp_sys_gpio_request 所以我们在 sys_config.fex中需要设置gpio的开启电平为低
```dts
lcd_bl_en           = port:PH15<1><0><default><0>
lcd_bl_en_power     = "none"
```
#### 代码处理上注意关闭的时候，要设置相反的电平
```c
static s32 disp_lcd_backlight_enable(struct disp_device *lcd)
{
	disp_gpio_set_t  gpio_info[1];
	struct disp_lcd_private_data *lcdp = disp_lcd_get_priv(lcd);
	unsigned long flags;

	if ((NULL == lcd) || (NULL == lcdp)) {
		DE_WRN("NULL hdl!\n");
		return DIS_FAIL;
	}

	spin_lock_irqsave(&lcd_data_lock, flags);
	if (lcdp->bl_enabled) {
		spin_unlock_irqrestore(&lcd_data_lock, flags);
		return -EBUSY;
	}

	lcdp->bl_need_enabled = 1;
	lcdp->bl_enabled = true;
	spin_unlock_irqrestore(&lcd_data_lock, flags);

	if (disp_lcd_is_used(lcd)) {
		unsigned bl;
		if (lcdp->lcd_cfg.lcd_bl_en_used) {
			//io-pad
			if (!((!strcmp(lcdp->lcd_cfg.lcd_bl_en_power, "")) || (!strcmp(lcdp->lcd_cfg.lcd_bl_en_power, "none"))))
				disp_sys_power_enable(lcdp->lcd_cfg.lcd_bl_en_power);

			memcpy(gpio_info, &(lcdp->lcd_cfg.lcd_bl_en), sizeof(disp_gpio_set_t));

			lcdp->lcd_cfg.lcd_bl_gpio_hdl =
			    disp_sys_gpio_request(gpio_info, 1);
		}
		bl = disp_lcd_get_bright(lcd);
		disp_lcd_set_bright(lcd, bl);
	}

	return 0;
}

static s32 disp_lcd_backlight_disable(struct disp_device *lcd)
{
	struct disp_lcd_private_data *lcdp = disp_lcd_get_priv(lcd);
	unsigned long flags;

	if ((NULL == lcd) || (NULL == lcdp)) {
		DE_WRN("NULL hdl!\n");
		return DIS_FAIL;
	}

	spin_lock_irqsave(&lcd_data_lock, flags);
	if (!lcdp->bl_enabled) {
		spin_unlock_irqrestore(&lcd_data_lock, flags);
		return -EBUSY;
	}

	lcdp->bl_enabled = false;
	spin_unlock_irqrestore(&lcd_data_lock, flags);

	if (disp_lcd_is_used(lcd)) {
		if (lcdp->lcd_cfg.lcd_bl_en_used) {
			disp_sys_gpio_set_value(lcdp->lcd_cfg.lcd_bl_gpio_hdl, 1, "lcd_bl_off"); //add by Devin
			disp_sys_gpio_release(lcdp->lcd_cfg.lcd_bl_gpio_hdl, 2);
			//io-pad
			if (!((!strcmp(lcdp->lcd_cfg.lcd_bl_en_power, "")) || (!strcmp(lcdp->lcd_cfg.lcd_bl_en_power, "none"))))
				disp_sys_power_disable(lcdp->lcd_cfg.lcd_bl_en_power);
		}
	}

	return 0;
}
```
#### 2. 屏power部分vcc 暂时不处理，考虑到某些桥芯片，需要与屏保持通信，产品对功耗不敏感可以暂时不关闭屏的电源，志关闭背光。如果必要实现可以修改相关流程
#### 在下面的代码中添加gpio控制即可。
```c
#if defined (CONFIG_FPGA_V4_PLATFORM) && defined (SUPPORT_EINK)
static s32 disp_lcd_power_enable(struct disp_device *lcd, u32 power_id)
{
	//add gpio 电平控制
	struct disp_lcd_private_data *lcdp = disp_lcd_get_priv(lcd);
	volatile unsigned long  *reg = 0;
	unsigned long val = 0;
	if ((NULL == lcd) || (NULL == lcdp)) {
		DE_WRN("NULL hdl!\n");
		return DIS_FAIL;
	}
	if (disp_lcd_is_used(lcd)) {
		reg = ((volatile unsigned long  *)0xf1c20878);
		val = readl(reg);
		val = val & 0x000fffff;
		val = val | 0x11100000;
		writel(val, reg);
		reg = ((volatile unsigned long  *)0xf1c2087C);
		val = readl(reg);
		val = val & 0x1fffffff;
		val = val | 0xe0000000;
		writel(val, reg);
		return 0;
	}
	return DIS_FAIL;
}
static s32 disp_lcd_power_disable(struct disp_device *lcd, u32 power_id)
{
		//add gpio 电平控制
	struct disp_lcd_private_data *lcdp = disp_lcd_get_priv(lcd);
	volatile unsigned long  *reg = 0;
	unsigned long val = 0;
	if ((NULL == lcd) || (NULL == lcdp)) {
		DE_WRN("NULL hdl!\n");
		return DIS_FAIL;
	}
	if (disp_lcd_is_used(lcd)) {
		reg = ((volatile unsigned long  *)0xf1c20878);
		val = readl(reg);
		val = val & 0x000fffff;
		val = val | 0x77700000;
		writel(val, reg);
		return 0;
	}
	return DIS_FAIL;
}
#else
static s32 disp_lcd_power_enable(struct disp_device *lcd, u32 power_id)
{
	struct disp_lcd_private_data *lcdp = disp_lcd_get_priv(lcd);

	if ((NULL == lcd) || (NULL == lcdp)) {
		DE_WRN("NULL hdl!\n");
		return DIS_FAIL;
	}

	if (disp_lcd_is_used(lcd)) {
		if (1 == lcdp->lcd_cfg.lcd_power_used[power_id]) {
			/* regulator type */
			disp_sys_power_enable(lcdp->lcd_cfg.lcd_power[power_id]);
		}
	}
	//add gpio 电平控制
	return 0;
}

static s32 disp_lcd_power_disable(struct disp_device *lcd, u32 power_id)
{
	struct disp_lcd_private_data *lcdp = disp_lcd_get_priv(lcd);

	if ((NULL == lcd) || (NULL == lcdp)) {
		DE_WRN("NULL hdl!\n");
		return DIS_FAIL;
	}
	
	if (disp_lcd_is_used(lcd)) {
		if (1 == lcdp->lcd_cfg.lcd_power_used[power_id]) {
			/* regulator type */
			disp_sys_power_disable(lcdp->lcd_cfg.lcd_power[power_id]);
		}
	}
		//add gpio 电平控制
	return 0;
}
#endif
```
