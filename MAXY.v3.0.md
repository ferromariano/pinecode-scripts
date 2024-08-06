# Moving Average ShiftXY MAXY

Este indicador nos permite desplazar la media móvil en el eje X y en el eje Y por eso se llama "MA X Y"

#### Code:

<details open>
  <summary>@version=5</summary>

```javascript
//@version=5
indicator(title="Moving Average ShiftXY MAXY", shorttitle="MAXY 3.0", overlay=true, timeframe="", timeframe_gaps=true)

//Global
len = input.int(100, minval=1, title="Length", group="Configuracion global")
src = input(close, title="Source", group="Configuracion global")

ma(source, length, type) =>
    switch type
        "SMA" => ta.sma(source, length)
        "EMA" => ta.ema(source, length)
        "SMMA (RMA)" => ta.rma(source, length)
        "WMA" => ta.wma(source, length)
        "VWMA" => ta.vwma(source, length)

inicioY1=1
offsetX = input.int(  title="X",  defval=0,        minval=-500, maxval=500,                             group="Primera Media", inline = 'l1', display=display.data_window)
offsetY = input.float(title="Y",  defval=inicioY1, minval=-500, maxval=99999999, step=0.01,             group="Primera Media", inline = 'l1', display=display.data_window)
typeMA = input.string(title = "", defval = "WMA",  options=["SMA", "EMA", "SMMA (RMA)", "WMA", "VWMA"], group="Primera Media", inline = 'l1', display=display.data_window)
out = ma(src, len,typeMA)*offsetY
plot(out, color=color.white, title="Primera media", offset=offsetX)

inicioY2=1.1
offsetX2 = input.int(  title="X",  defval=0,        minval=-500, maxval=500,                             group="Segunda Media", inline = 'l2', display=display.data_window)
offsetY2 = input.float(title="Y",  defval=inicioY2, minval=-500, maxval=99999999, step=0.01,             group="Segunda Media", inline = 'l2', display=display.data_window)
typeMA2 = input.string(title = "", defval = "WMA",  options=["SMA", "EMA", "SMMA (RMA)", "WMA", "VWMA"], group="Segunda Media", inline = 'l2', display=display.data_window)
out2 = ma(src, len,typeMA2)*offsetY2
plot(out2, color=color.white, title="Segunda media", offset=offsetX2)

inicioY3=.9
//Entrada de datos para la media movil 3
offsetX3 = input.int(  title="X",  defval=0,        minval=-500, maxval=500,                             group="Tercera Media", inline = 'l3', display=display.data_window)
offsetY3 = input.float(title="Y",  defval=inicioY3, minval=-500, maxval=99999999, step=0.01,             group="Tercera Media", inline = 'l3', display=display.data_window)
typeMA3 = input.string(title = "", defval = "WMA",  options=["SMA", "EMA", "SMMA (RMA)", "WMA", "VWMA"], group="Tercera Media", inline = 'l3', display=display.data_window)
out3 = ma(src, len,typeMA3)*offsetY3
plot(out3, color=color.white, title="Tercera media", offset=offsetX3)

inicioY4=0.5
//Entrada de datos para la media movil 4
offsetX4 = input.int(  title="X",  defval=0,        minval=-500, maxval=500,                             group="Cuarta Media", inline = 'l4', display=display.data_window)
offsetY4 = input.float(title="Y",  defval=inicioY4, minval=-500, maxval=99999999, step=0.01,             group="Cuarta Media", inline = 'l4', display=display.data_window)
typeMA4 = input.string(title = "", defval = "WMA",  options=["SMA", "EMA", "SMMA (RMA)", "WMA", "VWMA"], group="Cuarta Media", inline = 'l4', display=display.data_window)
out4 = ma(src, len,typeMA4)*offsetY4
plot(out4, color=color.white, title="Cuarta media", offset=offsetX4)

```
</details>
