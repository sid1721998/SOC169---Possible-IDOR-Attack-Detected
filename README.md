# SOC169---Possible-IDOR-Attack-Detected
SOC169 - Possible IDOR Attack Detected

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>SOC Incident Report</title>
<style>
    body {
        font-family: Arial, sans-serif;
        background-color: #f4f6f9;
        color: #2c3e50;
        margin: 0;
        padding: 20px;
    }
    .container {
        max-width: 1000px;
        margin: auto;
        background-color: #ffffff;
        padding: 30px;
        border-radius: 10px;
        box-shadow: 0 5px 15px rgba(0,0,0,0.1);
    }
    h1, h2, h3 {
        color: #2980b9;
        margin-bottom: 10px;
    }
    h1 {
        text-align: center;
        margin-bottom: 30px;
    }
    p, li {
        line-height: 1.6;
    }
    table {
        width: 100%;
        border-collapse: collapse;
        margin-bottom: 20px;
    }
    table, th, td {
        border: 1px solid #bdc3c7;
    }
    th {
        background-color: #ecf0f1;
        padding: 10px;
        text-align: left;
    }
    td {
        padding: 10px;
    }
    ul {
        margin-left: 20px;
    }
    .badge {
        display: inline-block;
        padding: 3px 8px;
        border-radius: 5px;
        color: #fff;
        font-size: 0.9em;
    }
    .success {
        background-color: #27ae60;
    }
    .note {
        background-color: #f1c40f;
        padding: 10px;
        border-radius: 5px;
        margin-top: 10px;
    }
    .step-table th {
        background-color: #3498db;
        color: #fff;
    }
</style>
</head>
<body>
<div class="container">
    <h1>Security Operations Center (SOC) Report</h1>
    <h3>Incident Title: Possible IDOR Attack on WebServer1005</h3>
    <p><strong>EventID:</strong> 119<br>
       <strong>Date/Time Detected:</strong> Feb 28, 2022, 10:48 PM<br>
       <strong>Analyst:</strong> [Analyst Name]</p>

    <h2>1. Summary of Incident</h2>
    <p>An external IP (<strong>134.209.118.137, DigitalOcean</strong>) made consecutive POST requests to <code>/get_user_info/</code> on <strong>WebServer1005 (172.16.17.15)</strong>. The requests incremented the <code>user_id</code> parameter, suggesting an IDOR (Insecure Direct Object Reference) attack. All requests returned <strong>HTTP 200</strong>, indicating the attacker successfully accessed multiple user records.</p>
    <p><strong>Purpose:</strong> Investigate, contain, and mitigate unauthorized access.</p>

    <h2>2. Affected Asset</h2>
    <table>
        <tr><th>Attribute</th><th>Value</th></tr>
        <tr><td>Hostname</td><td>WebServer1005</td></tr>
        <tr><td>IP Address</td><td>172.16.17.15</td></tr>
        <tr><td>Domain</td><td>letsdefend.local</td></tr>
        <tr><td>OS</td><td>Windows Server 2019, 64-bit</td></tr>
        <tr><td>Primary User</td><td>webadmin35</td></tr>
        <tr><td>Last Login</td><td>Feb 15, 2022, 01:43 PM</td></tr>
        <tr><td>Client/Server</td><td>Server</td></tr>
    </table>
    <p><em>Observation:</em> No suspicious processes or attack simulation tools were running. Internal activities (Docker deployment, SSL installation) are legitimate.</p>

    <h2>3. Traffic Analysis</h2>
    <table>
        <tr>
            <th>Time</th>
            <th>Source IP</th>
            <th>Destination IP</th>
            <th>POST Param</th>
            <th>HTTP Status</th>
            <th>Response Size</th>
        </tr>
        <tr><td>10:45 PM</td><td>134.209.118.137</td><td>172.16.17.15</td><td>user_id=2</td><td>200</td><td>253</td></tr>
        <tr><td>10:45 PM</td><td>134.209.118.137</td><td>172.16.17.15</td><td>user_id=1</td><td>200</td><td>188</td></tr>
        <tr><td>10:46 PM</td><td>134.209.118.137</td><td>172.16.17.15</td><td>user_id=3</td><td>200</td><td>351</td></tr>
        <tr><td>10:47 PM</td><td>134.209.118.137</td><td>172.16.17.15</td><td>user_id=4</td><td>200</td><td>158</td></tr>
        <tr><td>10:48 PM</td><td>134.209.118.137</td><td>172.16.17.15</td><td>user_id=5</td><td>200</td><td>267</td></tr>
    </table>
    <p><strong>Traffic Direction:</strong> Internet → Company network</p>

    <h2>4. Source IP Analysis</h2>
    <ul>
        <li>IP Owner: DigitalOcean, LLC (DO-13)</li>
        <li>Type: Cloud hosting / VPS</li>
        <li>Action: Check reputation via VirusTotal, AbuseIPDB, or Cisco Talos</li>
        <li>Note: External attacker, not part of internal testing</li>
    </ul>

    <h2>5. Attack Assessment</h2>
    <ul>
        <li>Attack Type: IDOR (Insecure Direct Object Reference)</li>
        <li>Success: <span class="badge success">Attack Succeeded</span></li>
        <li>Detection Method: Consecutive POST requests to <code>/get_user_info/</code> triggered SOC169 rule</li>
        <li>Evidence: HTTP 200 responses with varying sizes for different <code>user_id</code>s</li>
        <li>Conclusion: Sensitive user data was accessed without authorization</li>
    </ul>

    <h2>6. Containment Actions</h2>
    <ul>
        <li>Isolate Device: WebServer1005 contained via Endpoint Security</li>
        <li>Restrict Traffic: Blocked external IP 134.209.118.137</li>
        <li>Verify Containment: Device monitored and isolated from the Internet</li>
    </ul>
    <p><em>Explanation:</em> Containment prevents further data exposure and limits attacker activity.</p>

    <h2>7. Tier 2 Escalation</h2>
    <ul>
        <li>Action: Escalated to Tier 2</li>
        <li>Reason: Attack succeeded in accessing internal data; requires deeper investigation and mitigation</li>
        <li>Explanation: Escalation ensures proper remediation, deeper investigation, and follow-up reporting</li>
    </ul>

    <h2>8. Recommendations</h2>
    <ul>
        <li>Implement authorization checks for <code>user_id</code> access in <code>/get_user_info/</code></li>
        <li>Apply rate limiting to prevent sequential enumeration</li>
        <li>Deploy Web Application Firewall (WAF) rules to detect and block similar IDOR patterns</li>
        <li>Review application and system logs for other unauthorized access</li>
        <li>Report the malicious IP to DigitalOcean Abuse team</li>
    </ul>

    <h2>9. Analyst Note</h2>
    <p class="note">IDOR attack from external IP succeeded in accessing multiple user records; containment and Tier 2 escalation initiated.</p>

    <h2>10. Explanation of SOC Steps</h2>
    <table class="step-table">
        <tr><th>Step</th><th>Explanation</th></tr>
        <tr><td>Identify Alert</td><td>SOC169 triggered by sequential POST requests; first sign of possible IDOR.</td></tr>
        <tr><td>Traffic Analysis</td><td>Examined HTTP logs, response codes, and sizes to assess attack impact.</td></tr>
        <tr><td>Source IP Investigation</td><td>Determined external origin and ownership of IP (DigitalOcean).</td></tr>
        <tr><td>Success Check</td><td>Verified attack success by comparing response sizes and HTTP 200 codes.</td></tr>
        <tr><td>Containment</td><td>Isolated WebServer1005 to prevent further exploitation.</td></tr>
        <tr><td>Tier 2 Escalation</td><td>Escalated because attack succeeded, enabling deeper investigation and remediation.</td></tr>
        <tr><td>Recommendations</td><td>Implemented fixes, rate limits, and WAF rules to prevent recurrence.</td></tr>
    </table>

</div>
</body>
</html>
