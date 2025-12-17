# awk and sed – Quick Reference (DevOps)


---

## awk — Field & Data Processing Tool

Best for **column-based data**, logs, CSVs, and command outputs.

### Basic Syntax

```bash
awk 'pattern { action }' file
```

---

### Print Columns

```bash
echo "Pavan 25 AWS" | awk '{print $1, $3}'
```

Output:

```text
Pavan AWS
```

* `$1` → first column
* `$2` → second column
* `$NF` → last column
* `$0` → full line

---

### Custom Delimiter

```bash
awk -F',' '{print $1, $2}' users.csv
awk -F':' '{print $1, $3}' /etc/passwd
```

---

### Filtering with Conditions

```bash
awk '$2 > 80 {print $1, $2}' marks.txt
```

---

### Built-in Variables

```bash
awk '{print NR, NF, $0}' file
```

* `NR` → line number
* `NF` → number of fields

---

### DevOps Example – Disk Usage

```bash
df -h | awk '$5 > 80 {print $1, $5}'
```

---

## sed — Stream Editor

Best for **search, replace, delete, insert** operations.

### Basic Syntax

```bash
sed 'command' file
```

---

### Search and Replace

```bash
echo "Hello DevOps" | sed 's/DevOps/AWS/'
```

---

### Global Replace

```bash
sed 's/error/ERROR/g' app.log
```

---

### Delete Lines

```bash
sed '3d' file
sed '/DEBUG/d' app.log
```

---

### Print Specific Lines

```bash
sed -n '5p' file
sed -n '10,15p' file
```

---

### In-place Edit (Use Carefully)

```bash
sed -i 's/8080/9090/g' server.conf
```

---

## awk vs sed

| Feature                 | awk     | sed       |
| ----------------------- | ------- | --------- |
| Column-based processing | Yes     | No        |
| Conditions & math       | Strong  | Weak      |
| Search/replace          | Limited | Excellent |
| Edit files              | No      | Yes       |

---

## Combined Example

```bash
df -h | awk '$5 > 80 {print $1, $5}' | sed 's/%//'
```

---

## Interview / Real-world Examples

### Get usernames

```bash
awk -F':' '{print $1}' /etc/passwd
```

### Comment a config line

```bash
sed 's/^Listen/#Listen/' httpd.conf
```

---

### Quick Memory Tip

* **awk → columns & logic**
* **sed → find & replace**

---

✔ Suitable for GitHub README or notes repo
✔ Practical DevOps-focused examples
