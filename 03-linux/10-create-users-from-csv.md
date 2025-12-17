
## Question  
You’ve received a CSV file with a list of usernames and passwords to create users on a Linux system.  

**Task:**  
Write a shell script to read the CSV and:
- Create each user with the specified password.
- Force password change on first login.

```csv
username,password
alice,Password@123
bob,Secure@456
carol,DevOps@789
```

### 📝 Short Explanation  
This task tests your ability to read structured data (CSV) and automate user management using shell scripting — commonly required for onboarding automation in DevOps roles.

## ✅ Answer

```bash
#!/bin/bash

CSV_FILE="users.csv"

# Check if script is run as root
if [[ $EUID -ne 0 ]]; then
  echo "Please run this script as root."
  exit 1
fi

# Check if CSV file exists
if [[ ! -f "$CSV_FILE" ]]; then
  echo "CSV file not found: $CSV_FILE"
  exit 1
fi

# Skip header and read CSV
tail -n +2 "$CSV_FILE" | while IFS=',' read -r USERNAME PASSWORD
do
  # Trim spaces
  USERNAME=$(echo "$USERNAME" | xargs)
  PASSWORD=$(echo "$PASSWORD" | xargs)

  # Skip empty lines
  [[ -z "$USERNAME" || -z "$PASSWORD" ]] && continue

  # Check if user already exists
  if id "$USERNAME" &>/dev/null; then    #“&>/dev/null redirects both stdout and stderr, making it ideal for existence checks like id username. Using only >/dev/null hides stdout but still prints error messages to stderr, which is not suitable for clean automation.”
    echo "User already exists: $USERNAME"
    continue
  fi

  # Create user
  useradd -m "$USERNAME"

  # Set password
  echo "$USERNAME:$PASSWORD" | chpasswd

  echo "User created successfully: $USERNAME"
done
```
-------------

```bash
#!/bin/bash

INPUT="users.csv"

# Check if the file exists
if [[ ! -f "$INPUT" ]]; then
  echo "CSV file not found!"
  exit 1
fi

# Skip header and read each line
tail -n +2 "$INPUT" | while IFS=',' read -r username password; do
  # Check if user already exists
  if id "$username" &>/dev/null; then
    echo "User '$username' already exists. Skipping..."
    continue
  fi

  # Create the user
  useradd "$username"

  # Set the password
  echo "${username}:${password}" | chpasswd

  # Force password change on first login
  chage -d 0 "$username"

  echo "User '$username' created successfully."
done

```

---

### ✅ Script Usage:
Make it executable and run with root privileges:
```bash
chmod +x create_users.sh
sudo ./create_users.sh
```

---

### 🧠 How It Works:

- `IFS=',' read -r ...` parses each line into `username` and `password`.
- `useradd` creates the user.
- `chpasswd` sets the password using `echo "$username:$password"`.
- `chage -d 0` forces the user to reset their password on the first login.

> Summary:  
> This script reads a CSV file, creates users with the specified passwords, and ensures they are prompted to change their password at first login — a great example of secure onboarding automation.

---
