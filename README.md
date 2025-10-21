# SOC_LAB_Project
This lab simulates a SOC analyst workflow: detecting suspicious login attempts, investigating IPs, and documenting remediation.
docker start -ai soc_lab_test
su - postgres
# Switch to PostgreSQL user and start server
/usr/lib/postgresql/16/bin/initdb -D /var/lib/postgresql/data
/usr/lib/postgresql/16/bin/pg_ctl -D /var/lib/postgresql/data start
psql -U postgres
# Create database and table
CREATE DATABASE soc_logs;
\c soc_logs

CREATE TABLE login_attempts (
    id SERIAL PRIMARY KEY,
    time TIMESTAMP,
    ip VARCHAR(50),
    endpoint VARCHAR(100),
    status_code INT
);
# Insert sample logs
INSERT INTO login_attempts (time, ip, endpoint, status_code)
VALUES
('2025-10-17 09:00:01', '203.0.113.10', '/login.html', 200),
('2025-10-17 09:00:02', '203.0.113.10', '/login.html', 200),
('2025-10-17 09:00:03', '198.51.100.7', '/login.html', 404),
('2025-10-17 09:00:04', '203.0.113.10', '/login.html', 200),
('2025-10-17 09:00:05', '198.51.100.7', '/index.html', 200),
('2025-10-17 09:00:06', '10.0.0.5', '/index.html', 200),
('2025-10-17 09:00:07', '203.0.113.10', '/admin.html', 401),
('2025-10-17 09:00:08', '198.51.100.7', '/login.html', 404);
# Screenshot Placeholder

# Detecting Suspicious Activity
## Query to find multiple failed attempts per IP
SELECT ip, COUNT(*) AS failed_attempts
FROM login_attempts
WHERE status_code != 200
GROUP BY ip
HAVING COUNT(*) > 1;
# Screenshot Placeholder
# Investigating the IP
SELECT *
FROM login_attempts
WHERE ip = '198.51.100.7';

# Documenting the Incident
CREATE TABLE incident_report (
    id SERIAL PRIMARY KEY,
    ip VARCHAR(50),
    first_detected TIMESTAMP,
    last_detected TIMESTAMP,
    action_taken TEXT
);

INSERT INTO incident_report (ip, first_detected, last_detected, action_taken)
VALUES ('198.51.100.7', '2025-10-17 09:00:03', '2025-10-17 09:00:05', 'Blocked IP, monitored activity');
# Screenshot Placeholder

# List all failed logins:
SELECT ip, endpoint, status_code
FROM login_attempts
WHERE status_code != 200;
# Screenshot Placeholder

# Summary
Detected suspicious IP 198.51.100.7 with multiple failed logins

Investigated endpoints and confirmed no compromise

Documented incident in incident_report table

Workflow simulates real SOC analyst process: detection → investigation → remediation → documentation

# Outcome: Threat mitigated, documented, and ready for monitoring.

