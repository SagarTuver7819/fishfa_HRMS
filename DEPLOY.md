# Fishfa Rubbers HRMS — Git + Live Deploy

**Live subdomain:** https://your-hrms-domain.com/  
**GitHub:** https://github.com/SagarTuver7819/fishfa_HRMS

---

## 1) Local → GitHub push

```bash
cd c:\xampp\htdocs\fishfa_rubber
git add .
git status
git commit -m "Your message"
git push origin main
```

Important:
- `.env` git ma **nai** jase
- Live pe alag `.env` banavo

---

## 2) Live deploy — your-hrms-domain.com

### A. cPanel / Hosting
1. Subdomain confirm: `your-hrms-domain.com`
2. Document root = project folder (jya `index.php` che) — subdomain **root**, subfolder nahi
3. PHP **8.0+** enable
4. MySQL database + user create (note: DB name, user, password)
5. phpMyAdmin ma import:
   - `sql/hrms_database.sql`
   - `sql/employees.sql`
   - `sql/masters.sql`

### B. Code upload (ek method choose karo)

**Option 1 — Git (best)**
```bash
cd /home/USER/your-hrms-domain.com   # your document root
git clone https://github.com/SagarTuver7819/fishfa_HRMS.git .
# next updates:
git pull origin main
```

**Option 2 — ZIP**
- GitHub → Code → Download ZIP
- cPanel File Manager → extract in subdomain document root

### C. Live `.env` (server pe create karo)

```
APP_ENV=production
APP_URL=https://your-hrms-domain.com
APP_BASE_PATH=
DB_HOST=localhost
DB_PORT=3306
DB_NAME=your_live_db_name
DB_USER=your_live_db_user
DB_PASS=your_live_db_password
APP_TIMEZONE=Asia/Kolkata
```

`APP_BASE_PATH=` **empty** rakho (subdomain root mate).

### D. Permissions
```
assets/uploads/
assets/uploads/logo/
```
→ writable (`755` or `775`)

### E. Login check
- Open: https://your-hrms-domain.com/
- Admin: `admin` / `password123`
- HR: `hr` / `password123`
- Dashboard → Department → Employees / Masters

**Live pe password turat change kari lo.**

---

## 3) Local vs Live

| Item | Local | Live |
|------|-------|------|
| URL | http://localhost/fishfa_rubber | https://your-hrms-domain.com |
| APP_BASE_PATH | `/fishfa_rubber` | *(empty)* |
| DB | XAMPP `fishfa_rubber_hrms` | hosting DB |
| Config | local `.env` | live `.env` |

Code same — only `.env` alag.

---

## 5) Troubleshooting live

### CSS missing / plain HTML / links go to `/fishfa_rubber/...`
**Cause:** Live `.env` ma local values copy thai gaya.

Server `.env` aa rite hova joiye:
```
APP_ENV=production
APP_URL=https://your-hrms-domain.com
APP_BASE_PATH=
```

- Open **https://your-hrms-domain.com/** (NOT `/fishfa_rubber/`)
- `.env` save pachi browser hard refresh: `Ctrl+F5`
- Latest code: `git pull origin main` (production subdomain auto-fix included)

### 404 on `/fishfa_rubber/`
Live par aa folder nathi — subdomain root j use karo.

---

## 4) Safe update habit
1. Local test
2. `git push origin main`
3. Server: `git pull origin main`
4. Live `.env` touch nathi
5. SQL change hoy to phpMyAdmin ma manually run
6. Agar `hr` role missing hoy:

```sql
ALTER TABLE users MODIFY role ENUM('admin','hr','employee') NOT NULL DEFAULT 'hr';
INSERT INTO users (username, password, full_name, role, department_id, status)
VALUES ('hr', 'password123', 'HR Manager', 'hr', 2, 1)
ON DUPLICATE KEY UPDATE role='hr', password='password123';
```
