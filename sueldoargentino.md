# sueldo en dolares historico de argentina ( a chequear )

((suelto/max(precio_dolar, precio_dolar_adr))/(infla_usa/infla_usa.last)

sueldo           = **(ECONOMICS:ARWAG)**      sueldo en pesos  
precio_dolar     = **(FX_IDC:USDARS)**        precio dolar oficial  
precio_dolar_adr = **(BCBA:YPFD / NYSE:YPF)** precio dolar adr YPF ( desde el 2005 )  
infla_usa        = **(ECONOMICS:USCPI)**      ipc historico del dolar  
infla_usa.last   = **(ECONOMICS:USCPI)**      ultimo valor de la serie completa (314.18)  

#### Code:

<details open>
  <summary>@version=5</summary>

```javascript
//@version=5
indicator("Mi script")

suelto = request.security('ECONOMICS:ARWAG', timeframe.period,  close)
precio_dolar = request.security('FX_IDC:USDARS', timeframe.period,  close)

BCBA_YPFD = request.security('BCBA:YPFD', timeframe.period,  close)
NYSE_YPF = request.security('NYSE:YPF', timeframe.period,  close)
precio_dolar_adr = nz(BCBA_YPFD / NYSE_YPF, 0)

infla_usa = request.security('ECONOMICS:USCPI', timeframe.period,  close)

plot( ta.sma(((suelto/math.max(precio_dolar, precio_dolar_adr))/(infla_usa/314.18)), 6) )
```
</details>
