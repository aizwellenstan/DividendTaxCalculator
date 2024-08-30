```python
def calculate_tax(income):
    tax_brackets = [
        (560000, 0.05, 0),
        (1260000, 0.12, 39200),
        (2520000, 0.2, 140000),
        (4720000, 0.3, 392000),
        (float('inf'), 0.4, 864000)
    ]
    for limit, rate, deduction in tax_brackets:
        if income <= limit:
            return income * rate - deduction, limit
    return 0, float('inf')

def calculate_dividend_recommendation(monthly_salary):
    annual_salary = monthly_salary * 12
    exemption = 92000
    standard_deduction = 124000
    salary_deduction = 207000
    max_tax_credit = 80000
    
    net_income_no_dividend = annual_salary - (exemption + standard_deduction + salary_deduction)
    tax_no_dividend, _ = calculate_tax(net_income_no_dividend)
    
    def calculate_savings(dividend):
        net_income_with_dividend = annual_salary + dividend - (exemption + standard_deduction + salary_deduction)
        tax_with_dividend, _ = calculate_tax(net_income_with_dividend)
        dividend_tax_credit = min(dividend * 0.085, max_tax_credit)
        return tax_no_dividend - (tax_with_dividend - dividend_tax_credit)
    
    optimal_dividend = 0
    max_savings = 0
    for dividend in range(0, int(annual_salary), 1000):
        savings = calculate_savings(dividend)
        if savings > max_savings:
            max_savings = savings
            optimal_dividend = dividend

    return optimal_dividend

def combined_income_tax_formula(monthly_salary):
    dividend = calculate_dividend_recommendation(monthly_salary)
    annual_salary = monthly_salary * 12
    exemption = 92000
    standard_deduction = 124000
    salary_deduction = 207000
    max_tax_credit = 80000
    
    net_income = annual_salary + dividend - (exemption + standard_deduction + salary_deduction)
    tax_amount, current_bracket_limit = calculate_tax(net_income)
    dividend_tax_credit = min(dividend * 0.085, max_tax_credit)
    final_tax_amount = tax_amount - dividend_tax_credit

    # Determine the next bracket limit
    tax_brackets = [
        (560000, 0.05, 0),
        (1260000, 0.12, 39200),
        (2520000, 0.2, 140000),
        (4720000, 0.3, 392000),
        (float('inf'), 0.4, 864000)
    ]
    next_bracket_limit = next((limit for limit, _rate, _ded in tax_brackets if limit > net_income), float('inf'))
    
    # Calculate maximum dividend to avoid exceeding the next bracket limit
    max_dividend = next_bracket_limit - (annual_salary - exemption - standard_deduction - salary_deduction)
    
    output = f"""
    STEP1 算所得淨額： （薪資{annual_salary}+股利{dividend}）- （免稅額{exemption}+標準扣除額{standard_deduction}+薪資扣除額{salary_deduction}）={net_income:,}元
    STEP2 算應繳稅額：對照級距表後（落在{int(tax_amount/net_income*100)}%），算出應繳稅額。{net_income:,} X {int(tax_amount/net_income*100)}%={tax_amount:,.0f}元
    STEP3 算股利抵扣額：{dividend // 10000} x 8.5%={dividend_tax_credit/10000:,.2f}萬
    *注意股利抵扣額上限8萬！
    STEP4 兩者扣掉後得實際要繳的金額：{tax_amount:,.0f} – {dividend_tax_credit:,.0f}={final_tax_amount:,.0f}
    最大股利（防止升級到更高級距）：{max_dividend:,.0f}元
    """
    return output

# 執行並輸出結果
monthly_salary = 120000
print(combined_income_tax_formula(monthly_salary))
```
