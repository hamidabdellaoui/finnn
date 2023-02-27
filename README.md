# 
import QuantLib as ql

# Set up the market data
valuation_date = ql.Date(27, 2, 2023)
ql.Settings.instance().evaluationDate = valuation_date

spot_curve = ql.YieldTermStructureHandle(ql.FlatForward(valuation_date, 0.01, ql.Actual365Fixed()))
discount_curve = ql.YieldTermStructureHandle(ql.FlatForward(valuation_date, 0.02, ql.Actual365Fixed()))

# Set up the swap parameters
notional = 1000000
fixed_rate = 0.02
day_count = ql.Thirty360()
calendar = ql.UnitedStates()
payment_frequency = ql.Semiannual

start_date = ql.Date(27, 2, 2023)
end_date = ql.Date(27, 2, 2028)

fixed_schedule = ql.Schedule(
    start_date,
    end_date,
    payment_frequency,
    calendar,
    ql.ModifiedFollowing,
    ql.ModifiedFollowing,
    ql.DateGeneration.Backward,
    False
)

float_schedule = ql.Schedule(
    start_date,
    end_date,
    payment_frequency,
    calendar,
    ql.ModifiedFollowing,
    ql.ModifiedFollowing,
    ql.DateGeneration.Backward,
    False
)

index = ql.USDLibor(ql.Period(6, ql.Months), spot_curve)

# Create the swap
fixed_leg = ql.FixedRateLeg(fixed_schedule, day_count, [notional], [fixed_rate])
float_leg = ql.IborLeg([notional], float_schedule, index, day_count)

swap = ql.Swap(fixed_leg, float_leg)

# Price the swap
engine = ql.DiscountingSwapEngine(discount_curve)
swap.setPricingEngine(engine)

print("Swap NPV: ", swap.NPV())
