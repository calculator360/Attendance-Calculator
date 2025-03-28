
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Enhanced Attendance Calculator</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            background-color: #f5f5f5;
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }
        
        .container {
            width: 100%;
            max-width: 600px;
            padding: 30px;
            background: #222831;
            border-radius: 12px;
            box-shadow: 0 8px 20px rgba(0, 0, 0, 0.3);
            text-align: center;
        }
        
        h1 {
            color: #ffd700;
            margin-bottom: 30px;
        }
        
        .input-group {
            margin: 20px 0;
            text-align: left;
        }
        
        .input-group label {
            display: block;
            margin-bottom: 8px;
            font-weight: bold;
            color: #ffd700;
            font-size: 16px;
        }
        
        .input-group input {
            width: 100%;
            padding: 12px;
            font-size: 16px;
            color: #222831;
            border: none;
            border-radius: 6px;
            background-color: #eeeeee;
            transition: all 0.3s ease;
        }
        
        .input-group input:focus {
            outline: none;
            box-shadow: 0 0 0 2px #00adb5;
        }
        
        button {
            background: #00adb5;
            color: #eeeeee;
            border: none;
            padding: 14px 24px;
            margin-top: 10px;
            font-size: 16px;
            font-weight: bold;
            border-radius: 6px;
            cursor: pointer;
            transition: 0.3s ease;
            text-transform: uppercase;
        }
        
        button:hover {
            background: #007b8a;
            transform: translateY(-2px);
        }
        
        button:active {
            transform: translateY(0);
        }
        
        .result-container {
            margin-top: 30px;
            padding: 20px;
            border-radius: 8px;
            background-color: #393e46;
        }
        
        .result {
            margin-top: 20px;
            padding: 15px;
            background: #ffd700;
            color: #222831;
            font-weight: bold;
            border-radius: 6px;
            font-size: 18px;
        }
        
        .current-attendance {
            margin-top: 15px;
            color: #00adb5;
            font-weight: bold;
            font-size: 16px;
            padding: 10px;
        }
        
        .future-attendance {
            margin-top: 10px;
            color: #ff6347;
            font-weight: bold;
            font-size: 16px;
            padding: 10px;
        }
        
        .hidden {
            display: none;
        }
        
        .attendance-info {
            margin-top: 20px;
            text-align: left;
            color: #eeeeee;
            line-height: 1.6;
            padding: 15px;
            background-color: #292e36;
            border-radius: 6px;
        }
        
        @media (max-width: 768px) {
            .container {
                padding: 20px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Attendance Calculator</h1>
        
        <div class="input-group">
            <label for="required-percentage">Required Attendance Percentage (%):</label>
            <input type="number" id="required-percentage" value="75" min="1" max="100">
        </div>
        
        <div class="input-group">
            <label for="present-days">Days Present:</label>
            <input type="number" id="present-days" min="0" placeholder="Enter number of days attended">
        </div>
        
        <div class="input-group">
            <label for="total-days">Total Days:</label>
            <input type="number" id="total-days" min="1" placeholder="Enter total number of days">
        </div>
        
        <button onclick="calculateAttendance()">Calculate</button>
        
        <div class="result-container hidden" id="result-container">
            <div class="result" id="result">
                <!-- Results will be displayed here -->
            </div>
            
            <div class="current-attendance" id="current-attendance"></div>
            <div class="future-attendance" id="future-attendance"></div>
            
            <div class="attendance-info">
                <p><strong>Looking to maintain your attendance?</strong></p>
                <p>The calculation above shows the maximum number of future days you can miss while still maintaining the required attendance percentage.</p>
                <p>Remember: Regular attendance is crucial for success in both education and professional environments.</p>
            </div>
        </div>
    </div>
    
    <script>
        function calculateAttendance() {
            const requiredPercentage = parseFloat(document.getElementById("required-percentage").value);
            const presentDays = parseInt(document.getElementById("present-days").value);
            const totalDays = parseInt(document.getElementById("total-days").value);
            const resultContainer = document.getElementById("result-container");
            
            // Validate inputs
            if (isNaN(requiredPercentage) || isNaN(presentDays) || isNaN(totalDays) || 
                requiredPercentage <= 0 || requiredPercentage > 100 || 
                presentDays < 0 || totalDays < 1 || presentDays > totalDays) {
                alert("Please enter valid inputs. Make sure all fields are filled correctly.");
                return;
            }
            
            const currentAttendancePercentage = (presentDays / totalDays) * 100;
            
            // Check if current attendance is already below required
            if (currentAttendancePercentage < requiredPercentage) {
                resultContainer.classList.remove("hidden");
                document.getElementById("result").innerHTML = 
                    `You are below the required attendance percentage. You need to attend more classes.`;
                
                // Calculate how many consecutive days needed to reach required percentage
                let daysNeeded = 0;
                let tempTotalDays = totalDays;
                let tempPresentDays = presentDays;
                let tempAttendance = currentAttendancePercentage;
                
                while (tempAttendance < requiredPercentage) {
                    daysNeeded++;
                    tempTotalDays++;
                    tempPresentDays++;
                    tempAttendance = (tempPresentDays / tempTotalDays) * 100;
                }
                
                document.getElementById("current-attendance").innerHTML = 
                    `Current Attendance: ${presentDays}/${totalDays} → <strong>${currentAttendancePercentage.toFixed(2)}%</strong>`;
                
                document.getElementById("future-attendance").innerHTML = 
                    `You need to attend <strong>${daysNeeded}</strong> more consecutive days to reach the required ${requiredPercentage}% attendance.`;
                
                return;
            }
            
            let maxBunkDays = 0;
            let futureAttendance = currentAttendancePercentage;
            
            let futureTotalDays = totalDays;
            let futurePresentDays = presentDays;
            
            // Calculate max bunk days until attendance is slightly above required
            while (futureAttendance >= requiredPercentage) {
                maxBunkDays++;
                futureTotalDays++;
                futureAttendance = (futurePresentDays / futureTotalDays) * 100;
            }
            
            // Adjust back to the last valid bunk day
            maxBunkDays--;
            futureTotalDays = totalDays + maxBunkDays;
            futureAttendance = (presentDays / futureTotalDays) * 100;
            
            // Display results
            resultContainer.classList.remove("hidden");
            document.getElementById("result").innerHTML = 
                `You can miss <strong>${maxBunkDays}</strong> more days while maintaining required attendance.`;
            
            document.getElementById("current-attendance").innerHTML = 
                `Current Attendance: ${presentDays}/${totalDays} → <strong>${currentAttendancePercentage.toFixed(2)}%</strong>`;
            
            document.getElementById("future-attendance").innerHTML = 
                `After missing days: ${presentDays}/${futureTotalDays} → <strong>${futureAttendance.toFixed(2)}%</strong>`;
        }
    </script>
       <p>Maintaining a good attendance record is essential for students, employees, and institutions. A <strong>Attendance Calculator</strong> helps track attendance percentage based on the number of classes or workdays attended.</p>

        <h2 style="color: #d6336c;">What is an Attendance Calculator?</h2>
        <p>An <strong>Attendance Calculator</strong> is an online tool that calculates attendance percentage using:</p>
        <ul>
            <li>Total classes or workdays.</li>
            <li>Number of days attended.</li>
            <li>Required minimum attendance percentage.</li>
        </ul>

        <h2 style="color: #d6336c;">How to Use an Attendance Calculator?</h2>
        <p>Using an Attendance Calculator is simple:</p>
        <ul>
            <li>Enter the total number of classes or workdays.</li>
            <li>Enter the number of classes or workdays attended.</li>
            <li>The calculator provides the attendance percentage instantly.</li>
        </ul>

        <h2 style="color: #d6336c;">Why Use an Attendance Calculator?</h2>
        <p>Tracking attendance manually can be difficult. An <strong>Attendance Calculator</strong> helps by:</p>
        <ul>
            <li>Providing accurate attendance percentage.</li>
            <li>Helping students check if they meet the required attendance criteria.</li>
            <li>Assisting professionals in monitoring their work attendance.</li>
        </ul>

        <h2 style="color: #d6336c;">Conclusion</h2>
        <p>An <strong>Attendance Calculator</strong> is a valuable tool for students and employees. It ensures they meet attendance requirements and helps in proper planning to avoid attendance shortages.</p>

        <div class="footer">
    <p>&copy; <script>document.write(new Date().getFullYear())</script> Mini web Tool. All Rights Reserved. | 
    Powered by <a href="https://www.miniwebtool.live/" target="_blank">MiniWebTool</a></p>
</div>
</body>
</html>
