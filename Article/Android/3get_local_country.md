# 关于获取用户所在国家信息
在开发中一个需求，要求手机应用根据用户所在地区切换温度显示单位，在特定地区使用华摄氏度来显示。如何获取用户所在国家地区成为需要去思考的问题，本地获取还是远程获取以及通过SIM手机卡上的运营商信息获取。
1. 根据手机系统语言
```
getResources().getConfiguration().locale.getCountry()
```
像Android原生手机中语言设置有许多，单单是English下面就可以选择很多国家。[可以看这里](https://blog.csdn.net/qy070104070028/article/details/30485011)

2. SIM卡的地区信息
```
try {
            TelephonyManager tm = (TelephonyManager)context.getSystemService(Context.TELEPHONY_SERVICE);
            if(tm.getPhoneType() != 2) {
                return tm.getSimCountryIso().toUpperCase();
            }
        } catch (Exception var3) {
            ;
        }
```
>getSimCountryIso
String getSimCountryIso ()      
 Returns the ISO country code equivalent for the SIM provider's country code.

由于手上没有国外手机卡无法验证是否和系统语言一样可以获取到具体的国家Code。不过通过方法字面意思是返回国家对应的IsoCode。[可以通过这里查看](http://www.nationsonline.org/oneworld/country_code_list.htm)

# 补充说明
每个手机运营商都有一组：Mobile Country Code (MCC)和Mobile Network Code (MNC)。这两者也是运营商唯一标示Public Lang Mobile Network (PLMN)凭证。
例如一下方法就是获取SIM卡运营商的MNC，在原项目中使用其实是有问题的，无法作为国家地区精确判断。
```
try {
            TelephonyManager tm = (TelephonyManager)context.getSystemService("phone");
            if(tm.getPhoneType() != 2) {
                return tm.getNetworkCountryIso().toUpperCase();
            }
        } catch (Exception var3) {
            ;
        }
```