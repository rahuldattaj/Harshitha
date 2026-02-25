# Harshitha
Health predictor
from flask import Flask, render_template, request

app = Flask(__name__)


# Home page
@app.route("/")
def home():
    return render_template("index.html")


# Prediction function
@app.route("/predict", methods=["POST"])
def predict():

    # Get general inputs
    age = int(request.form.get("age"))
    bmi = float(request.form.get("bmi"))
    bp = int(request.form.get("bp"))
    cholesterol = int(request.form.get("cholesterol"))
    gender = request.form.get("gender")
    family_history = request.form.get("family_history")
    activity = request.form.get("activity")

    # Get female inputs
    periods_regular = request.form.get("periods_regular")
    cycle_length = request.form.get("cycle_length")
    weight_gain = request.form.get("weight_gain")
    acne = request.form.get("acne")
    pregnancy = request.form.get("pregnancy")

    # Initialize scores
    heart_risk = 0
    diabetes_risk = 0
    pcos_risk = 0

    # Heart risk logic
    if age > 45:
        heart_risk += 2
    if bp > 140:
        heart_risk += 2
    if cholesterol > 240:
        heart_risk += 2
    if family_history == "Yes":
        heart_risk += 1
    if activity == "Low":
        heart_risk += 1


    # Diabetes risk logic
    if bmi > 30:
        diabetes_risk += 2
    if activity == "Low":
        diabetes_risk += 1
    if family_history == "Yes":
        diabetes_risk += 1


    # PCOS risk logic (Female only)
    if gender == "Female":

        if periods_regular == "No":
            pcos_risk += 2

        if cycle_length and int(cycle_length) > 35:
            pcos_risk += 2

        if weight_gain == "Yes":
            pcos_risk += 1

        if acne == "Yes":
            pcos_risk += 1


    # Risk level classification
    def get_level(score):

        if score >= 4:
            return "High"
        elif score >= 2:
            return "Moderate"
        else:
            return "Low"


    heart_level = get_level(heart_risk)
    diabetes_level = get_level(diabetes_risk)
    pcos_level = get_level(pcos_risk)


    # Final result message
    result = f"""
Heart Disease Risk: {heart_level}

Diabetes Risk: {diabetes_level}
"""

    if gender == "Female":
        result += f"\nPCOS Risk: {pcos_level}"


    # Preventive advice
    advice = "\n\nPreventive Advice:\n"

    if heart_level == "High":
        advice += "- Monitor BP regularly\n"

    if diabetes_level == "High":
        advice += "- Reduce sugar intake\n"

    if pcos_level == "High":
        advice += "- Consult gynecologist\n"

    advice += "- Exercise regularly\n"
    advice += "- Maintain healthy diet\n"

    result += advice


    return render_template("index.html", result=result)


# Run server
if __name__ == "__main__":
    app.run(debug=True)
