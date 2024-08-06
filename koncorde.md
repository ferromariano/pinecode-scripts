# koncorde

Para el que no sabe que es : https://www.blai5.net/blai5-koncorde-que-es-y-como-usarlo/


#### Code:

<details open>
  <summary>@version=5</summary>

```javascript
//@version=5
// Al conocido Koncorde, le añade ver el codigo y poder tocar las varaibles.
// 
// Pueden leer sobre el funcionamiento del mismo en 
// - https://www.farobursatil.com/como-usar-indicador-koncorde-y-patrones/
// REF de los plots:
// [b]Señal: [/b] Es una EMA de Marron
// [b]Marron:[/b] Es ( RSI + MFI + BandasBollinger.Osc() + ( STOCH / 3 ) ) / 2
// [b]Verde:[/b] Usa valumen espesificamente NVI ( indicador de volúmenes negativos o Negative volume index )
// [b]Azul:[/b] Usa valumen espesificamente PVI (  indicador de volúmenes positivos o Positive Volume Index )



indicator(title="Koncorde - Code - mFerro", shorttitle="Koncorde", timeframe="", timeframe_gaps=true)

calc_mfi(length) =>
    src=hlc3
    upper = math.sum(volume * (ta.change(src) <= 0 ? 0 : src), length)
    lower = math.sum(volume * (ta.change(src) >= 0 ? 0 : src), length)
    ratio= upper / lower
    //ta.rsi(upper, lower)
    aa = 100 - (100/(1+ratio))

calc_marron(cm_xrsi, cm_xmf, cm_BollOsc, cm_stoc ) => 
    (cm_xrsi + cm_xmf + cm_BollOsc + (cm_stoc / 3))/2

tprice=input.source(title = "Source General", defval = ohlc4)
media_leng=input.int(title = "Media Genesal leng", defval = 15, minval = 1, step =  1)

//
// MARRON y MEDIA calc
//
xrsi_leng = input.int(title = "XRSI leng", defval = 14, minval = 1, maxval = 255, step =  1, group="MARRON")
xmf_leng = input.int(title = "XMF leng", defval = 14, minval = 1, maxval = 255, step =  1, group="MARRON")
basis_len=input.int(title = "BollOsc - basis leng", defval = 25, minval = 1, maxval = 255, step =  1, group="MARRON")
dev_len=input.int(title = "BollOsc - dev leng", defval = 25, minval = 1, maxval = 255, step =  1, group="MARRON")
mult=input(title = "BollOsc - dev mult", defval = 2.0, group="MARRON")
OB1_div=input(title = "BollOsc - OB1 div", defval = 2.0, group="MARRON")
stoch_len=input.int(title = "Stoch - leng", defval = 21, minval = 1, maxval = 255, step =  1, group="MARRON")
stoch_len_smooth=input.int(title = "BollOsc - Smooth Fast D", defval = 3, minval = 1, maxval = 255, step =  1, group="MARRON")

//
// AZUL input 
//
nvi_leng = input.int(title = "NVI length", defval = 90, minval = 1, maxval = 500, step =  1, group="AZUL")
nvi_signalleng = input.int(title = "NVI señal leng", defval = 15, minval = 1, maxval = 255, step =  1, group="AZUL")


//
// MARRON y MEDIA calc
//
xrsi = ta.rsi(source=tprice, length=xrsi_leng)
xmf = calc_mfi(xmf_leng)
basis = ta.sma(source=tprice, length=basis_len)
dev = mult * ta.stdev(source=tprice, length=dev_len)
upper = basis + dev
lower = basis - dev
OB1 = (upper + lower) / OB1_div
OB2 = upper - lower
BollOsc = ((tprice - OB1) / OB2 ) * 100

stoc = ta.sma(source=ta.stoch(source=tprice, high=high, low=low, length=stoch_len), length=stoch_len_smooth)

RS_MARRON = calc_marron( xrsi, xmf, BollOsc, stoc)
RS_MEDIA = ta.ema(source=RS_MARRON, length=media_leng)

//
// AZUL calc
//
nvim = ta.ema(ta.nvi, nvi_signalleng)
nvimax = ta.highest(nvim, nvi_leng)
nvimin = ta.lowest(nvim, nvi_leng)
RS_AZUL = (ta.nvi - nvim) * 100/ (nvimax - nvimin)

//
// VERDE calc
//
pvim = ta.ema(ta.pvi, media_leng)
pvimax = ta.highest(pvim, 90)
pvimin = ta.lowest(pvim, 90)
oscp = (ta.pvi - pvim) * 100/ (pvimax - pvimin)

RS_VERDE = RS_MARRON + oscp

//
// * * * * * *  * PLOT
//

plot(RS_VERDE, color=color.rgb(99, 226, 105), style=plot.style_area, title="VERDE AREA")
plot(RS_MARRON, color=#feeb9e, style=plot.style_area, title="MARRON area")
plot(RS_AZUL, color=#81BECE, style=plot.style_area, title="AZUL area")
plot(RS_MARRON, color=#F7C815, style=plot.style_line, title="MARRON linea", linewidth=2)
plot(RS_VERDE, color=#1A2902, style=plot.style_line, title="VERDE linea", linewidth=2)
plot(RS_AZUL, color=#378BA4, style=plot.style_line, title="AZUL linea", linewidth=2)
plot(RS_MEDIA, color=color.red, title="MEDIA", style=plot.style_line, linewidth=2)

hline(0,color=color.blue)
```
</details>
