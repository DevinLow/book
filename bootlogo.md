### 开机logo的用户可选实现
#### 1. 全志A40i ANDROID 7.1.1的平台，会将bootloader 分区挂载到文件系统，且具备读写属性。而开机logo就存在这个位置。/bootloader/bootlogo.bmp  
#### 也就是说，只要我们能修改这个文件，就等于我们能修改开机logo.自定义开机动画与开机音乐同理，存储位于/system/media/, 修改这个文件，就能达到目的。这里  
#### 我们主要讲解bootlogo 的可选实现方式

#### 1.首先我们需要将客户需要预装的开机logo ，安装到系统中。在vendor/device 中添加一个目录存放需要预装的文件，再利用product mk 将文件打包到system.img
```shell
PRODUCT_COPY_FILES += \
    $(call find-copy-subdir-files,*,$(LOCAL_PATH)/botologo,system/etc/bootlogo)
```
#### 2.在display_settings.xml 中增加布局
```xml
        <ListPreference
                android:key="bootlogo_select"
                android:title="@string/bootlogo_select"
                android:summary="@string/bootlogo_summary"
                android:entries="@array/bootlogo_entries"
                android:entryValues="@array/bootlogo_values" />
```
#### 3.在custom.xml 中添加list资源内容
```xml
 </string-array>
        <string-array name="bootlogo_entries">
	<item>default</item>
        <item>Acura</item>
        <item>Audi</item>
        <item>BAIC</item>
        <item>Bentley</item>
	<item>BMW</item>
        <item>Brabus</item>
        <item>Buick</item>
        <item>BYD</item>
        <item>Cadillac</item>
	<item>CCAG</item>
	<item>Chery</item>
	<item>Chevrolet</item>
	<item>Chrysler</item>
	<item>Citroen</item>
	<item>Dodge</item>
	<item>Fiat</item>
	<item>Ford</item>
	<item>Geely</item>
	<item>GMC</item>
	<item>Great Wall</item>
	<item>Haval</item>
	<item>Honda</item>
	<item>Hyundai</item>
	<item>Isuzu</item>
	<item>JAC</item>
	<item>Jaguar</item>
	<item>JEEP</item>
	<item>Kia</item>
	<item>Lamborghini</item>
	<item>Lexus</item>
	<item>Lifan</item>
	<item>Lincoln</item>
	<item>Luxgen</item>
	<item>Maserati</item>
	<item>Mazda</item>
	<item>Mercedes-Benz</item>
	<item>MG</item>
	<item>Mitsubishi</item>
	<item>Nissan</item>	
	<item>Opel</item>
	<item>Peugeot</item>
	<item>Porsche</item>
	<item>Range Rover</item>
	<item>Renault</item>
	<item>Roewe</item>
	<item>Rolls-Royce</item>
	<item>SGMW</item>
	<item>Skoda</item>
	<item>Ssang Yong</item>
	<item>Suzuki</item>
	<item>TATA</item>
	<item>Toyota</item>
	<item>Volkswagen</item>
	<item>Volvo</item>
    </string-array>
    <!--bootlogo array-->
    <string-array name="bootlogo_values">
        <item>0</item>
        <item>1</item>
        <item>2</item> 
        <item>3</item> 
	<item>4</item>
        <item>5</item>
        <item>6</item>
        <item>7</item> 
        <item>8</item> 
	<item>9</item>
        <item>10</item>
        <item>11</item>
        <item>12</item> 
        <item>13</item> 
	<item>14</item>
        <item>15</item>
        <item>16</item>
        <item>17</item> 
        <item>18</item> 
	<item>19</item>
        <item>20</item>
        <item>21</item>
        <item>22</item> 
        <item>23</item> 
	<item>24</item>
        <item>25</item>
        <item>26</item>
        <item>27</item> 
        <item>28</item> 
	<item>29</item>
        <item>30</item>
        <item>31</item>
        <item>32</item> 
        <item>33</item> 
	<item>34</item>
        <item>35</item>
        <item>36</item>
        <item>37</item> 
        <item>38</item> 
	<item>39</item>
        <item>40</item>
        <item>41</item>
        <item>42</item> 
        <item>43</item> 
	<item>44</item>
        <item>45</item>
        <item>46</item>
        <item>47</item> 
        <item>48</item> 
	<item>49</item>
        <item>50</item>
        <item>51</item>
        <item>52</item> 
        <item>53</item> 
	<item>54</item>
	<item>55</item>
    </string-array>
```
#### 4.添加系统默认属性Settings.System.BOOTLOGO_SHOW，实现参考上一节内容[添加自定义默认系统属性](./setptiv.md)    
#### 5.在DisplaySettings.java 中增加处理
```java
  //添加定义
     private static final String KEY_BOOT_LOGO = "bootlogo_select";
     private ListPreference mBootlogoPreference;
//初始化
	mBootlogoPreference = (ListPreference) findPreference(KEY_BOOT_LOGO);
	if (mBootlogoPreference != null) {
	    int v =Settings.System.getInt(resolver, Settings.System.BOOTLOGO_SHOW, 1);
	    mBootlogoPreference.setValue(String.valueOf(v));
            mBootlogoPreference.setOnPreferenceChangeListener(this); //设置点击监听
        }

//监听事件处理
//    @Override
//    public boolean onPreferenceChange(Preference preference, Object objValue) {
        if (preference == mNightModePreference) {
            try {
                final int value = Integer.parseInt((String) objValue);
                final UiModeManager uiManager = (UiModeManager) getSystemService(
                        Context.UI_MODE_SERVICE);
                uiManager.setNightMode(value);
            } catch (NumberFormatException e) {
                Log.e(TAG, "could not persist night mode setting", e);
            }
        }
	if (preference == mBootlogoPreference) {
            try {
                final int value = Integer.parseInt((String) objValue);
		setbootlogo(value);
		Settings.System.putInt(getContentResolver(),Settings.System.BOOTLOGO_SHOW,value);
            } catch (NumberFormatException e) {
                Log.e(TAG, "could not persist night mode setting", e);
            }
        }
//实现替换开机logo动作
    private boolean copyFile(File src, File dst) {
	long inSize = src.length();
	long outSize = 0;
	try {
	    if (!dst.exists()) {
		return false;
	    }
	    FileInputStream in = new FileInputStream(src);
	    FileOutputStream out = new FileOutputStream(dst);
	    int length = -1;
	    byte[] buf = new byte[1024];
	    while ((length = in.read(buf)) != -1) {
		out.write(buf, 0, length);
		outSize += length;
		int temp = (int) (((float) outSize) / inSize * 100);
	    }
	    out.flush();
	    in.close();
	    out.close();
	} catch (FileNotFoundException e) {
	    e.printStackTrace();
	    return false;
	} catch (IOException e) {
	    e.printStackTrace();
	    return false;
	}
	return true;
    }
    private void setbootlogo(int v){
	CharSequence[] chars = mBootlogoPreference.getEntries();
	String str =chars[v].toString();
	if(copyFile(new File("/system/etc/bootlogo/"+str+".bmp"),new File("/bootloader/bootlogo.bmp"))){
		//do nothings	
	}else{
		Toast.makeText(mContext,"bootlogo set fail your mast set it again",Toast.LENGTH_LONG);
	}
    }

```
