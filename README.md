# notifier.py
import psycopg2

DB_PARAMS = {
    "dbname": "notifier_db",
    "user": "postgres",
    "password": "password",
    "host": "localhost",
    "port": 5432
}

def send_notification(user_id, message):
    conn = psycopg2.connect(**DB_PARAMS)
    cur = conn.cursor()
    cur.execute("INSERT INTO notifications(user_id, message) VALUES (%s,%s);", (user_id, message))
    conn.commit()
    cur.close()
    conn.close()
    print(f"Notification sent to {user_id}: {message}")

def get_unread(user_id):
    conn = psycopg2.connect(**DB_PARAMS)
    cur = conn.cursor()
    cur.execute("SELECT id, message FROM notifications WHERE user_id=%s AND read=false;", (user_id,))
    messages = cur.fetchall()
    cur.close()
    conn.close()
    return messages

if __name__ == "__main__":
    send_notification("user123", "Your report is ready")
    print("Unread:", get_unread("user123"))
