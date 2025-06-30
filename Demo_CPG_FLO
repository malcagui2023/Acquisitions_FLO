# streamlit_app.py
import streamlit as st
import csv
from datetime import datetime

def calculate_acquisition_fit(lead):
    score = 0.0
    if lead.get("estimated_revenue"):
        revenue_str = lead["estimated_revenue"].replace("$", "").replace("M", "000000").replace("B", "000000000").replace("-", "").strip()
        if ' ' in revenue_str:
            min_rev_str = revenue_str.split(' ')[0]
            max_rev_str = revenue_str.split(' ')[-1]
            try:
                min_revenue = float(min_rev_str)
                max_revenue = float(max_rev_str)
                avg_revenue = (min_revenue + max_revenue) / 2
            except ValueError:
                avg_revenue = 0
        else:
            try:
                avg_revenue = float(revenue_str)
            except ValueError:
                avg_revenue = 0
        if avg_revenue > 5000000:
            score += 0.3
    if lead.get("estimated_employee_count"):
        emp_count_str = str(lead["estimated_employee_count"]).replace("-", " ").strip()
        if ' ' in emp_count_str:
            min_emp_str = emp_count_str.split(' ')[0]
            max_emp_str = emp_count_str.split(' ')[-1]
            try:
                min_employees = int(min_emp_str)
                max_employees = int(max_emp_str)
                avg_employees = (min_employees + max_employees) / 2
            except ValueError:
                avg_employees = 0
        else:
            try:
                avg_employees = int(emp_count_str)
            except ValueError:
                avg_employees = 0
        if avg_employees > 50:
            score += 0.2
    if "Dessert" in ", ".join(lead.get("sector_tags", [])) or "Bakery" in ", ".join(lead.get("sector_tags", [])):
        score += 0.25
    return f"{score:.2f}"

def generate_leads():
    return [
        {
            "company_name": "Smiley Honey",
            "business_phone": "(850) 639-5672",
            "decision_maker_name": "Brian Bertonneau",
            "cell_phone_number": None,
            "verified_business_email": "info@smileyhoney.com",
            "brief_business_description": "Smiley Honey is a family-owned honey and beekeeping business...",
            "estimated_employee_count": "<25",
            "estimated_revenue": "<$5 Million",
            "website_url": "https://www.smileyhoney.com/",
            "location": "Wewahitchka, Florida",
            "sector_tags": ["Food products", "Food manufacturer"],
            "match_confidence": 0.95
        },
        # Add other leads here...
    ]

st.title("Florida Food Manufacturers - Acquisition Fit Analyzer")
leads = generate_leads()

data = []
for lead in leads:
    required_fields = [
        "company_name", "business_phone", "decision_maker_name", "verified_business_email",
        "brief_business_description", "estimated_employee_count", "estimated_revenue",
        "website_url", "location"
    ]
    populated_fields = sum(1 for field in required_fields if lead.get(field))
    completeness_score = (populated_fields / len(required_fields)) * 100
    acquisition_fit_score = calculate_acquisition_fit(lead)
    data.append({
        "Company Name": lead.get("company_name"),
        "Phone": lead.get("business_phone"),
        "Decision Maker": lead.get("decision_maker_name"),
        "Email": lead.get("verified_business_email"),
        "Employees": lead.get("estimated_employee_count"),
        "Revenue": lead.get("estimated_revenue"),
        "Fit Score": acquisition_fit_score,
        "Completeness": f"{completeness_score:.1f}%",
        "Confidence": lead.get("match_confidence"),
        "URL": lead.get("website_url")
    })

st.dataframe(data)

csv_name = "output.csv"
with open(csv_name, mode="w", newline="", encoding="utf-8") as f:
    writer = csv.DictWriter(f, fieldnames=data[0].keys())
    writer.writeheader()
    writer.writerows(data)

with open(csv_name, "rb") as f:
    st.download_button(label="📥 Download CSV", data=f, file_name=csv_name, mime="text/csv")
