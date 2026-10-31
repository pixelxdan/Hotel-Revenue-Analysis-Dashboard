# 📄 Executive Summary

A hotel group with 25 properties across Delhi, Mumbai, Hyderabad, and Bangalore needed a single view of revenue, occupancy, pricing, and booking realisation to see where performance is strong and where revenue is being lost.

Using **134,590 bookings** (1 May - 31 Jul 2022), a Power BI dashboard was built on a multi-fact star-schema model with 26 DAX measures. Performance was analyzed by city, hotel category, room class, booking platform, and weekday vs weekend.

### Key Findings
* **Overall Performance**: Revenue was ₹1.71 billion, with an ADR of ₹12,696, RevPAR of ₹7,347, and occupancy of 57.9%. Performance was stable across May, June, and July.
* **Revenue Leakage**: Realisation was 70.1%. 24.8% of bookings were cancelled and 5.0% were no-shows, leaving about ₹299M (14.9%) of generated revenue unrealised.
* **Mumbai Leads**: Mumbai drives 39.1% of revenue and has the highest ADR (₹15,387) and RevPAR (₹8,907). Delhi has the highest occupancy (60.5%) but the smallest revenue share (17.2%). Hyderabad has the lowest ADR (₹9,322).
* **Luxury Drives Revenue, Business Earns More Per Room**: Luxury hotels generate 61.6% of revenue, but they are 16 of the 25 hotels. Business hotels have a slightly higher RevPAR (₹7,498 vs ₹7,256).
* **Weekend Demand Is Not Priced In**: Weekend occupancy is 73.6% versus 51.3% on weekdays, but ADR is almost identical (₹12,724 vs ₹12,679).
* **Premium Rooms Punch Above Their Weight**: Presidential rooms are 11.9% of bookings but 22.0% of revenue.
* **Cancellations Are Systemic**: Realisation stays between 69.8% and 70.6% across every platform and between 69.9% and 70.3% across cities, so the issue is not tied to one channel or location.

### Recommendations
1. **Raise weekend rates**, since occupancy is already 73.6% and ADR is flat versus weekdays.
2. **Build weekday demand** with packages and corporate offers to lift the 51.3% occupancy.
3. **Cut cancellations at the policy level** using deposits, non-refundable rates, or tighter cancellation windows.
4. **Review Hyderabad pricing**, which has solid occupancy but the lowest ADR.
5. **Push Presidential and Premium rooms**, which earn the highest ADR.
6. **Grow direct bookings**, which are only 15.0% of revenue.

### Limitations
* The data covers only 3 months, so year-over-year comparison and seasonality are not possible.
* There is no cost or profit data, so profitability cannot be measured.
* Only 42.1% of bookings have a rating (average 3.62), so rating analysis is limited.
* 40.9% of revenue comes from the "others" platform group, which hides channel detail.
