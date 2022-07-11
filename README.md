// This source code is subject to the terms of the Mozilla Public License 2.0 at https://mozilla.org/MPL/2.0/
// © KivancOzbilgic

//@version=4
study("ST0P", overlay = true)
Percent = input(title="Percent", type=input.float, defval=3)
src = input(close, title="Source")
changeLONGSHORT = input(title="LONG=1 SHORT=2", type=input.integer, minval=1, maxval=2, defval=1)
KADEME= input(title="Unit", type=input.float,step=0.1, defval=0.0)
showsignals = input(title="Show ST0P Signals ?", type=input.bool, defval=true)
highlighting = input(title="Highlighter On/Off ?", type=input.bool, defval=true)
SHORT=src+KADEME+src*Percent/100
SHORTprev=nz(SHORT[1],SHORT)
SUB = 0.0
SUB := SHORT<nz(SUB[1]) or src[1]>nz(SUB[1])  ? SHORT : nz(SUB[1])
LONG=src-KADEME-src*Percent/100
LONGprev=nz(LONG[1],LONG)
SLB = 0.0
SLB := LONG>nz(SLB[1]) or src[1]<nz(SLB[1]) ? LONG : nz(SLB[1])
ST0P = changeLONGSHORT==1 ? SLB : SUB
longFillColor = highlighting ? (changeLONGSHORT==1 ? color.green : color.white) : color.white
shortFillColor = highlighting ? (changeLONGSHORT==2 ? color.red : color.white) : color.white
col12 = ST0P > ST0P[1]
col32 = ST0P < ST0P[1]
color2 = col12 ? color.green : col32 ? color.red : #001AE1

fill(plot(ohlc4, title="", style=plot.style_circles, linewidth=0), plot(ST0P,color=color2,linewidth=3), title="LONG Highligter", color=longFillColor)
fill(plot(ohlc4, title="", style=plot.style_circles, linewidth=0), plot(ST0P,color=color2,linewidth=3), title="SHORT Highligter", color=shortFillColor)
alertcondition(changeLONGSHORT==1 and crossunder(src,ST0P), title="LONG POSITION ST0P!", message="SELL!")
alertcondition(changeLONGSHORT==2 and crossover(src,ST0P), title="SHORT POSITION ST0P!", message="BUY!")
plotshape(changeLONGSHORT==2 and crossover(src,ST0P) and showsignals ? SHORT : na, title="Buy", text="ST0P", location=location.absolute, style=shape.labelup, size=size.tiny, color=color.green, textcolor=color.white, transp=0)
plotshape(changeLONGSHORT==1 and crossunder(src,ST0P) and showsignals ? LONG : na, title="Sell", text="ST0P", location=location.absolute, style=shape.labeldown, size=size.tiny, color=color.red, textcolor=color.white, transp=0)
