from decimal import Decimal, ROUND_HALF_UP
import plotly.graph_objs as go
import plotly.offline as pof
import matplotlib.pyplot as plt
import numpy as np
from IPython.display import display
from ipywidgets import interact
import ipywidgets as widgets

def EPP(principal, monthly_rate, months):
    #本金平均攤還
    all11=0
    EPP_p_=[]
    EPP_i_=[]

    p_pay=principal/months
    rest_p=principal
    pay_per_month=[]
    print("如依本金平均攤還計算(四捨五入):\n")
    for i in range(months):


        if i==months-1:

            total_pay=int(Decimal(str(rest_p*(1+monthly_rate))).quantize(Decimal('1'), rounding=ROUND_HALF_UP))
            i_pay=total_pay-p_pay
            all11+=total_pay
            EPP_p_.append(p_pay)
            EPP_i_.append(i_pay)
            pay_per_month.append(total_pay)
            print(f"共應繳{all11}元\n")
        else:
            i_pay=rest_p*monthly_rate
            total_pay=int(Decimal(str(p_pay+i_pay)).quantize(Decimal('1'), rounding=ROUND_HALF_UP))
            rest_p-=p_pay
            all11+=total_pay
            EPP_p_.append(p_pay)
            EPP_i_.append(i_pay)
            pay_per_month.append(total_pay)
    return pay_per_month, all11, EPP_p_, EPP_i_

def ETP(p, r, n):
    #本利平均攤還
    all22=0
    ETP_p_=[]
    ETP_i_=[]
    p_pay=p/n
    rest_p=p
    pay_per_month=[]
    print("如依本利平均攤還計算(四捨五入):\n")
    PMT=p*(r*((1+r)**n))/((1+r)**n-1)
    actual_PMT=int(Decimal(str(PMT)).quantize(Decimal('1'), rounding=ROUND_HALF_UP))
    for i in range(n):
        if i==n-1:
            i_pay=int(Decimal(str(rest_p*r)).quantize(Decimal('1'), rounding=ROUND_HALF_UP))
            p_pay=rest_p
            total_pay=i_pay+p_pay
            all22+=total_pay
            ETP_p_.append(p_pay)
            ETP_i_.append(i_pay)
            pay_per_month.append(total_pay)
            print(f"共應繳{all22}元\n")
        else:
            i_pay=int(Decimal(str(rest_p*r)).quantize(Decimal('1'), rounding=ROUND_HALF_UP))
            p_pay=actual_PMT-i_pay
            pay_per_month.append(actual_PMT)
            ETP_p_.append(p_pay)
            ETP_i_.append(i_pay)

            rest_p-=p_pay
            all22+=actual_PMT
    return pay_per_month, all22, ETP_p_, ETP_i_


def gogogo():
    principal=int(input("請輸入本金金額:"))
    yearly_rate=int(input("請輸入年利率(%):"))
    months=int(input("請輸入分期月份數(月):"))
    all1=0
    all2=0
    EPP_p=[]
    EPP_i=[]
    ETP_p=[]
    ETP_i=[]


    monthly_rate=yearly_rate/12/100
    EPP_list, all1, EPP_p, EPP_i=EPP(principal, monthly_rate, months)
    ETP_list, all2, ETP_p, ETP_i=ETP(principal, monthly_rate, months)
    x_list=[x for x in range(1, months+1)]
    print(f"本利平均攤還(ETP)比本金平均攤還(EPP)的總共繳交金額多了{all2-all1}元。")
    categories = ["EPP", "ETP"]
    values = [all1, all2]

    import plotly.io as pio
    import plotly.express as px
    import plotly.graph_objects as go

    # 1. 設定渲染器
    pio.renderers.default = "colab"

        # 建立空白圖表物件
    fig1 = go.Figure()

    # 加入第一條線
    fig1.add_trace(
        go.Scatter(
            x=x_list,
            y=EPP_list,
            mode='lines+markers',
            name='本金平均攤還每月金額',
            hovertemplate='X: %{x}<br>Y: %{y:,}<extra></extra>',# 圖例名稱
        )
    )

    # 加入第二條線
    fig1.add_trace(
        go.Scatter(
            x=x_list,
            y=ETP_list,
            mode='lines+markers',
            name='本利平均攤還每月金額',
            hovertemplate='X: %{x}<br>Y: %{y:,}<extra></extra>',# 圖例名稱
        )
    )

    # 設定標題與座標軸
    fig1.update_layout(
        title='EPP & ETP之每月支付金額比較', xaxis_title='分期', yaxis_title='金額'
    )
    fig1.show()
    fig2 = go.Figure()

    # 加入第一條線
    fig2.add_trace(
        go.Scatter(
            x=x_list,
            y=EPP_p,
            mode='lines+markers',
            name='本金平均攤還每月支付本金',
            hovertemplate='X: %{x}<br>Y: %{y:,}<extra></extra>',# 圖例名稱
        )
    )

    # 加入第二條線
    fig2.add_trace(
        go.Scatter(
            x=x_list,
            y=EPP_i,
            mode='lines+markers',
            name='本金平均攤還每月支付利息',
            hovertemplate='X: %{x}<br>Y: %{y:,}<extra></extra>',# 圖例名稱
        )
    )
    fig2.add_trace(
        go.Scatter(
            x=x_list,
            y=ETP_i,
            mode='lines+markers',
            name='本利平均攤還每月支付利息',
            hovertemplate='X: %{x}<br>Y: %{y:,}<extra></extra>',# 圖例名稱
        )
    )
    fig2.add_trace(
        go.Scatter(
            x=x_list,
            y=ETP_p,
            mode='lines+markers',
            name='本利平均攤還每月支付本金',
            hovertemplate='X: %{x}<br>Y: %{y:,}<extra></extra>',# 圖例名稱
        )
    )

    # 設定標題與座標軸
    fig2.update_layout(
        title='EPP & ETP之本金，利息每月支付金額比較', xaxis_title='分期', yaxis_title='金額'
    )
    fig2.show()
    import pandas as pd

    # 1. 輸入您手邊的兩個數字
    data = {
        '類別': ['EPP 應繳總額', 'ETP 應繳總額'],
        '金額': [all1, all2]  # 換成您的實際數字
    }
    df = pd.DataFrame(data)

    # 2. 建立長條圖
    fig = px.bar(
        df,
        x='類別',
        y='金額',
        color='類別',  # 讓兩根柱子擁有不同顏色
        title='EPP 與 ETP 應繳總額比較',
        text_auto=",.0f",
        # 直接在柱子上方顯示數值
    )
    fig.update_traces(
    hovertemplate='<b>%{x}</b><br>金額: %{y:,.0f}<extra></extra>'
    )
    # 3. 顯示圖表
    fig.show()
gogogo()
