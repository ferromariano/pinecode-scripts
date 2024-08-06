# koncorde multi timeframe

- Para el que no sabe que es koncorde aca: https://www.blai5.net/blai5-koncorde-que-es-y-como-usarlo/
- esta version te deja ver simultaneamente varios timeframe
- para resumir la informacion solo se muestra linea marron y media roja
- y se le agrego un histograma


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

indicator(title="Koncorde - multiTime - mFerro", shorttitle="Koncorde multiTime")

calc_mfi(length) =>
    upper = math.sum(volume * (ta.change(hlc3) <= 0 ? 0 : hlc3), length)
    lower = math.sum(volume * (ta.change(hlc3) >= 0 ? 0 : hlc3), length)
    ratio= upper / lower
    //ta.rsi(upper, lower)
    aa = 100 - (100/(1+ratio))

calc_marron(cm_xrsi, cm_xmf, cm_BollOsc, cm_stoc ) => 
    (cm_xrsi + cm_xmf + cm_BollOsc + (cm_stoc / 3))/2

//
// MARRON
//
calc_m(xrsi_leng, xmf_leng, basis_len, mult, dev_len, OB1_div, stoch_len, stoch_len_smooth) => 
    xrsi = ta.rsi(source=ohlc4, length=xrsi_leng)
    xmf = calc_mfi(xmf_leng)
    basis = ta.sma(source=ohlc4, length=basis_len)
    dev = mult * ta.stdev(source=ohlc4, length=dev_len)
    upper = basis + dev
    lower = basis - dev
    OB1 = (upper + lower) / OB1_div
    OB2 = upper - lower
    BollOsc = ((ohlc4 - OB1) / OB2 ) * 100
    stoc = ta.sma(source=ta.stoch(source=ohlc4, high=high, low=low, length=stoch_len), length=stoch_len_smooth)
    rs = calc_marron( xrsi, xmf, BollOsc, stoc)

calc_m_bytime(divisor, media_leng, xrsi_leng, xmf_leng, basis_len, mult, dev_len, OB1_div, stoch_len, stoch_len_smooth ) =>
    RS_MARRON = calc_m(xrsi_leng, xmf_leng, basis_len, mult, dev_len, OB1_div, stoch_len, stoch_len_smooth)
    RS_MEDIA = ta.ema(source=RS_MARRON, length=media_leng)
    rs_hist = (RS_MARRON - RS_MEDIA)+divisor
    [RS_MARRON+divisor, RS_MEDIA+divisor, rs_hist]


view_m1_i =input.bool(title = "View 1m", defval = true)
view_hist_m1_i=input.bool(title = "View hist 1m", defval = true)
view_line_m1_i=input.bool(title = "View lines 1m", defval = true)
view_hist_m1=view_m1_i ? view_hist_m1_i : false
view_line_m1=view_m1_i ? view_line_m1_i : false
view_base_m1=view_m1_i ? (view_hist_m1 or view_line_m1) ? true : false : false

view_5m_i =input.bool(title = "View 5m", defval = true)
view_hist_5m_i=input.bool(title = "View hist 5m", defval = true)
view_line_5m_i=input.bool(title = "View lines 5m", defval = true)
view_hist_5m=view_5m_i ? view_hist_5m_i : false
view_line_5m=view_5m_i ? view_line_5m_i : false
view_base_5m=view_5m_i ? (view_hist_5m or view_line_5m) ? true : false : false

view_15m_i =input.bool(title = "View 15m", defval = true)
view_hist_15m_i=input.bool(title = "View hist 15m", defval = true)
view_line_15m_i=input.bool(title = "View lines 15m", defval = true)
view_hist_15m=view_15m_i ? view_hist_15m_i : false
view_line_15m=view_15m_i ? view_line_15m_i : false
view_base_15m=view_15m_i ? (view_hist_15m or view_line_15m) ? true : false : false

view_1h_i =input.bool(title = "View 1h", defval = true)
view_hist_1h_i=input.bool(title = "View hist 1h", defval = true)
view_line_1h_i=input.bool(title = "View lines 1h", defval = true)
view_hist_1h=view_1h_i ? view_hist_1h_i : false
view_line_1h=view_1h_i ? view_line_1h_i : false
view_base_1h=view_1h_i ? (view_hist_1h or view_line_1h) ? true : false : false

view_4h_i =input.bool(title = "View 4h", defval = true)
view_hist_4h_i=input.bool(title = "View hist 4h", defval = true)
view_line_4h_i=input.bool(title = "View lines 4h", defval = true)
view_hist_4h=view_4h_i ? view_hist_4h_i : false
view_line_4h=view_4h_i ? view_line_4h_i : false
view_base_4h=view_4h_i ? (view_hist_4h or view_line_4h) ? true : false : false

view_1d_i =input.bool(title = "View 1d", defval = true)
view_hist_1d_i=input.bool(title = "View hist 1d", defval = true)
view_line_1d_i=input.bool(title = "View lines 1d", defval = true)
view_hist_1d=view_1d_i ? view_hist_1d_i : false
view_line_1d=view_1d_i ? view_line_1d_i : false
view_base_1d=view_1d_i ? (view_hist_1d or view_line_1d) ? true : false : false





divisor=input.int(title = "divisor", defval = 200, minval = 1, step =  10)

tprice=input.source(title = "Source General", defval = ohlc4)
media_leng=input.int(title = "Media Genesal leng", defval = 15, minval = 1, step =  1)
xrsi_leng = input.int(title = "XRSI leng", defval = 14, minval = 1, maxval = 255, step =  1, group="MARRON")
xmf_leng = input.int(title = "XMF leng", defval = 14, minval = 1, maxval = 255, step =  1, group="MARRON")
basis_len=input.int(title = "BollOsc - basis leng", defval = 25, minval = 1, maxval = 255, step =  1, group="MARRON")
dev_len=input.int(title = "BollOsc - dev leng", defval = 25, minval = 1, maxval = 255, step =  1, group="MARRON")
mult=input(title = "BollOsc - dev mult", defval = 2.0, group="MARRON")
OB1_div=input(title = "BollOsc - OB1 div", defval = 2.0, group="MARRON")
stoch_len=input.int(title = "Stoch - leng", defval = 21, minval = 1, maxval = 255, step =  1, group="MARRON")
stoch_len_smooth=input.int(title = "BollOsc - Smooth Fast D", defval = 3, minval = 1, maxval = 255, step =  1, group="MARRON")


divisor_1 = 0
[ma_1, me_1, hist_1] = request.security(syminfo.tickerid, '1', calc_m_bytime(divisor_1, media_leng, xrsi_leng, xmf_leng, basis_len, mult, dev_len, OB1_div, stoch_len, stoch_len_smooth ) )
var label_1 = label.new(x=na, y=divisor_1, color=color.black, textcolor=color.white, style=label.style_label_left, size=size.small, text='1')
plot( view_hist_m1 ? hist_1 : na, title = "hist 1m", style =  plot.style_histogram, histbase=divisor_1, color = (hist_1 >= divisor_1 ? (hist_1[1] <= hist_1 ? #26A69A : #B2DFDB) : (hist_1[1] <= hist_1 ? #FFCDD2 : #FF5252)), linewidth=3)
plot( view_line_m1 ? ma_1 : na, title = "marron 1m",    style=plot.style_line, histbase=divisor_1, color = #F7C815, linewidth=1)
plot( view_line_m1 ? me_1 : na, title = "media 1m", style=plot.style_line, histbase=divisor_1, color = color.red, linewidth=1)
hline(view_base_m1 ? divisor_1 : na,color=color.blue)
if barstate.islast and view_base_m1
    label.set_x(label_1, last_bar_index+2)
    label.set_color(label_1, (hist_1 >= divisor_1 ? #26A69A : #FF5252) )

divisor_5 = (view_base_5m ? divisor : 0) + divisor_1
[ma_5, me_5, hist_5] = request.security(syminfo.tickerid, '5', calc_m_bytime( divisor_5, media_leng, xrsi_leng, xmf_leng, basis_len, mult, dev_len, OB1_div, stoch_len, stoch_len_smooth ) )
var label_5 = label.new(x=na, y=divisor_5, color=color.black, textcolor=color.white, style=label.style_label_left, size=size.small, text='5')
plot( view_hist_5m ? hist_5 : na, title = "hist 5m", style =  plot.style_histogram, histbase=divisor_5, color = (hist_5 >= divisor_5 ? (hist_5[1] < hist_5 ? #26A69A : #B2DFDB) : (hist_5[1] < hist_5 ? #FFCDD2 : #FF5252)), linewidth=3)
plot( view_line_5m ? ma_5 : na, title = "marron 5m",    style=plot.style_line, histbase=divisor_5, color = #F7C815, linewidth=1)
plot( view_line_5m ? me_5 : na, title = "media 5m", style=plot.style_line, histbase=divisor_5, color = color.red, linewidth=1)
hline(view_base_5m ? divisor_5 : na,color=color.blue)
if barstate.islast and view_base_5m
    label.set_x(label_5, last_bar_index+2)
    label.set_color(label_5, (hist_5 >= divisor_5 ? #26A69A : #FF5252) )

divisor_15 = (view_base_15m ? divisor : 0) + divisor_5
[ma_15,me_15, hist_15] = request.security(syminfo.tickerid, '15', calc_m_bytime( divisor_15, media_leng, xrsi_leng, xmf_leng, basis_len, mult, dev_len, OB1_div, stoch_len, stoch_len_smooth ) )
var label_15 = label.new(x=na, y=divisor_15, color=color.black, textcolor=color.white, style=label.style_label_left, size=size.small, text='15')
plot( view_hist_15m ? hist_15 : na, title = "hist 15m", style =  plot.style_histogram, histbase=divisor_15, color = (hist_15 >= divisor_15 ? (hist_15[1] < hist_15 ? #26A69A : #B2DFDB) : (hist_15[1] < hist_15 ? #FFCDD2 : #FF5252)), linewidth=3)
plot( view_line_15m ? ma_15 : na, title = "marron 15m",   style=plot.style_line, histbase=divisor_15, color = #F7C815, linewidth=1)
plot( view_line_15m ? me_15 : na, title = "media 15m",    style=plot.style_line, histbase=divisor_15, color = color.red, linewidth=1)
hline(view_base_15m ? divisor_15 : na,color=color.blue)
if barstate.islast and view_base_15m
    label.set_x(label_15, last_bar_index+2)
    label.set_color(label_15, (hist_15 >= divisor_15 ? #26A69A : #FF5252) )

divisor_60 = (view_base_1h ? divisor : 0) + divisor_15
[ma_60,me_60, hist_60] = request.security(syminfo.tickerid, '60', calc_m_bytime( divisor_60, media_leng, xrsi_leng, xmf_leng, basis_len, mult, dev_len, OB1_div, stoch_len, stoch_len_smooth ) )
var label_60 = label.new(x=na, y=divisor_60, color=color.black, textcolor=color.white, style=label.style_label_left, size=size.small, text='60')
plot( view_hist_1h ? hist_60 : na, title = "hist 1h", style =  plot.style_histogram, histbase=divisor_60, color = (hist_60 >= divisor_60 ? (hist_60[1] < hist_60 ? #26A69A : #B2DFDB) : (hist_60[1] < hist_60 ? #FFCDD2 : #FF5252)), linewidth=3)
plot( view_line_1h ? ma_60 : na, title = "marron 1h",   style=plot.style_line, histbase=divisor_60, color = #F7C815, linewidth=1)
plot( view_line_1h ? me_60 : na, title = "media 1h",    style=plot.style_line, histbase=divisor_60, color = color.red, linewidth=1)
hline(view_base_1h ? divisor_60 : na,color=color.blue)
if barstate.islast and view_base_1h
    label.set_x(label_60, last_bar_index+2)
    label.set_color(label_60, (hist_60 >= divisor_60 ? #26A69A : #FF5252) )

divisor_4h = (view_base_4h ? divisor : 0) + divisor_60
[ma_4h,me_4h, hist_4h] = request.security(syminfo.tickerid, '240', calc_m_bytime( divisor_4h, media_leng, xrsi_leng, xmf_leng, basis_len, mult, dev_len, OB1_div, stoch_len, stoch_len_smooth ) )
var label_4h = label.new(x=na, y=divisor_4h, color=color.black, textcolor=color.white, style=label.style_label_left, size=size.small, text='240')
plot( view_hist_4h ? hist_4h : na, title = "hist 4h", style =  plot.style_histogram, histbase=divisor_4h, color = (hist_4h >= divisor_4h ? (hist_4h[1] < hist_4h ? #26A69A : #B2DFDB) : (hist_4h[1] < hist_4h ? #FFCDD2 : #FF5252)), linewidth=3)
plot( view_line_4h ? ma_4h : na, title = "marron 4h",   style=plot.style_line, histbase=divisor_4h, color = #F7C815, linewidth=1)
plot( view_line_4h ? me_4h : na, title = "media 4h",    style=plot.style_line, histbase=divisor_4h, color = color.red, linewidth=1)
hline(view_base_4h ? divisor_4h : na,color=color.blue)
if barstate.islast and view_base_4h
    label.set_x(label_4h, last_bar_index+2)
    label.set_color(label_4h, (hist_4h >= divisor_4h ? #26A69A : #FF5252) )

divisor_1d = (view_base_1d ? divisor : 0) + divisor_4h
[ma_1d,me_1d, hist_1d] = request.security(syminfo.tickerid, '1D', calc_m_bytime( divisor_1d, media_leng, xrsi_leng, xmf_leng, basis_len, mult, dev_len, OB1_div, stoch_len, stoch_len_smooth ) )
var label_1d = label.new(x=na, y=divisor_1d, color=color.black, textcolor=color.white, style=label.style_label_left, size=size.small, text='1D')
plot( view_hist_1d ? hist_1d : na, title = "hist 1D", style =  plot.style_histogram, histbase=divisor_1d, color = (hist_1d >= divisor_1d ? (hist_1d[1] < hist_1d ? #26A69A : #B2DFDB) : (hist_1d[1] < hist_1d ? #FFCDD2 : #FF5252)), linewidth=3)
plot( view_line_1d ? ma_1d : na, title = "marron 1D",   style=plot.style_line, histbase=divisor_1d, color = #F7C815, linewidth=1)
plot( view_line_1d ? me_1d : na, title = "media 1D",    style=plot.style_line, histbase=divisor_1d, color = color.red, linewidth=1)
hline(view_base_1d ? divisor_1d : na,color=color.blue)
if barstate.islast and view_base_1d
    label.set_x(label_1d, last_bar_index+2)
    label.set_color(label_1d, (hist_1d >= divisor_1d ? #26A69A : #FF5252) )

```
</details>
