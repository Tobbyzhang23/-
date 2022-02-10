def initialize(context):
    # 中证1000
    set_benchmark('399300.XSHE')
    # 动态复权
    set_option('use_real_price', True)
    # 输出内容到日志 log.info()
    log.info('初始函数开始运行且全局只运行一次')
    # 手续费
    set_order_cost(OrderCost(close_tax=0.001, open_commission=0.0003, close_commission=0.0003, min_commission=5), type='stock')
    
    # 获取指数
    g.security = get_index_stocks('399300.XSHE')
    
    g.q = query(valuation).filter(valuation.code.in_(g.security))
    g.N = 20    # 20只股票
    
    run_monthly(handle, 1)     # 第一个参数是对应的函数，第二个参数指第几个交易日
    
def handle(context):
    df = get_fundamentals(g.q)[['code', 'market_cap']]     # 花式索引选出股票代码和市值
    df = df.sort_values("market_cap").iloc[:g.N,:]   # pandas排序函数，将数据集依照某个字段中的数据进行排序
    
    # 期待持有的股票
    to_hold = df['code'].values
    for stock in context.portfolio.positions:
        if stock not in to_hold:
            # 目标股数下单,卖出非标的的股票
            order_target(stock, 0)
    
    # 期待持有且还未持仓的股票
    to_buy = [stock for stock in to_hold if stock not in context.portfolio.positions]
    if len(to_buy) > 0:  # 调仓
        # 每只股票预计投入的资金
        cash_per_stock = context.portfolio.available_cash / len(to_buy)
        for stock in to_buy:
            # 按价值下单，买入需买入的股票
            order_value(stock, cash_per_stock)
