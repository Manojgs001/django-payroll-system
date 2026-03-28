# Django Payroll Management System

> A full-stack web application for managing employee payrolls with role-based dashboards and real-time analytics.

**🌐 Live Demo:** [https://manojgs.pythonanywhere.com](https://manojgs.pythonanywhere.com)  
**💻 Source Code:** You're looking at it!

---

## About This Project

I built this payroll management system as a team project to solve real-world HR challenges. The idea was simple: create a system where admins, HR managers, and employees each have their own customized experience, with the right tools for their specific needs.

What started as a basic CRUD app evolved into a complete payroll solution with interactive dashboards, automated salary calculations, and secure role-based access control. Along the way, I learned a lot about Django's authentication system, database optimization, and building intuitive user interfaces.

---

## What It Does

### For Admins 👑
Admins get the bird's-eye view with analytics and full system control:
- Summary statistics across all employees and payrolls
- Visual charts showing paid vs unpaid payrolls (using Chart.js)
- Complete oversight of employee data and salary structures
- Access to Django's powerful admin panel

### For HR Managers 💼
HR folks need to manage people and process payroll efficiently:
- Add, edit, or deactivate employees
- Process monthly payrolls and mark them as paid
- Update salary components (basic pay, HRA, allowances, deductions)
- Quick search and filtering to find specific employees or payrolls
- Track pending payments at a glance

### For Employees 👨‍💼
Employees want transparency about their compensation:
- View detailed salary breakdown
- Access payroll history month by month
- Preview payslips before they're finalized
- No access to other employees' data (privacy first!)

---

## Screenshots

<p align="center">
  <img src="screenshots/dashboard.png" alt="Dashboard" width="45%">
  <img src="screenshots/payroll.png" alt="Payroll Management" width="45%">
</p>

<p align="center">
  <img src="screenshots/employee-list.png" alt="Employee List" width="45%">
  <img src="screenshots/salary-structure.png" alt="Salary Structure" width="45%">
</p>

> **Note:** Add your screenshots to a `screenshots/` folder in the repository

---

## Tech Stack

I chose Django because it handles authentication and database management really well out of the box. For the frontend, I went with Bootstrap to keep things clean and responsive without spending weeks on custom CSS.

**Backend:**
- Django 5.0.2 (Python web framework)
- SQLite for development, MySQL ready for production
- Custom decorators for role-based permissions

**Frontend:**
- Bootstrap 5.3 for responsive UI
- Vanilla JavaScript for interactivity (search, sorting, filters)
- Chart.js for data visualization

**Deployment:**
- Hosted on PythonAnywhere
- Version control with Git & GitHub

---

## Quick Start

Want to try it out? Here's how to get it running on your machine:

### Prerequisites
- Python 3.8 or higher
- pip (Python package manager)
- Git

### Installation

**1. Clone the repository**
```bash
git clone https://github.com/YOUR-USERNAME/django-payroll-system.git
cd django-payroll-system
```

**2. Set up a virtual environment**
```bash
python -m venv venv

# On Windows:
venv\Scripts\activate

# On Mac/Linux:
source venv/bin/activate
```

**3. Install dependencies**
```bash
pip install -r requirements.txt
```

**4. Set up the database**
```bash
python manage.py migrate
```

**5. Create a superuser account**
```bash
python manage.py createsuperuser
```

**6. (Optional) Load some test data**
```bash
python manage.py seed_fake
```

**7. Run the development server**
```bash
python manage.py runserver
```

**8. Open your browser**

Visit `http://127.0.0.1:8000/` and you're good to go!

**Default test accounts** (if you used seed_fake):
- Admin: `admin` / `admin123.`
- HR: `hr` / `hr123.`
- Employee: `emp` / `emp123.`

---

## Key Features I'm Proud Of

### 1. Custom Authentication System
Instead of using Django's complex built-in permissions, I created a simple `@role_required` decorator that checks user roles before rendering views. Much cleaner!

```python
@role_required(['admin', 'hr'])
def manage_employees(request):
    # Only admins and HR can access this
    employees = Employee.objects.filter(status='active')
    return render(request, 'employees.html', {'employees': employees})
```

### 2. Smart Salary Calculations
Salaries are calculated on-the-fly using Django's `@property` decorator. This means no redundant data storage and the numbers are always accurate:

```python
class SalaryStructure(models.Model):
    basic = models.DecimalField(max_digits=12, decimal_places=2)
    hra = models.DecimalField(max_digits=12, decimal_places=2, default=0)
    allowances = models.DecimalField(max_digits=12, decimal_places=2, default=0)
    deductions = models.DecimalField(max_digits=12, decimal_places=2, default=0)
    
    @property
    def net_salary(self):
        return (self.basic + self.hra + self.allowances) - self.deductions
```

### 3. Interactive Dashboards
Each role sees a completely different dashboard tailored to their needs. The admin dashboard includes a doughnut chart that updates in real-time as payrolls are marked paid.

### 4. Real-Time Search
You can search for employees or payroll by name or department without refreshing the page. I implemented this with vanilla JavaScript to keep things lightweight.

---

## Project Structure

```
django-payroll-system/
│
├── payroll/                 # Main project folder
│   ├── settings.py         # Django configuration
│   ├── urls.py             # URL routing
│   └── wsgi.py             # Deployment config
│
├── myapp/                   # Core application
│   ├── models.py           # Database models
│   ├── views.py            # View functions
│   ├── forms.py            # Django forms
│   ├── decorators.py       # Custom @role_required
│   ├── signals.py          # Auto-create user profiles
│   └── management/
│       └── commands/
│           └── seed_fake.py  # Generate test data
│
├── templates/               # HTML templates
│   ├── base.html           # Base layout
│   ├── dashboard.html      # Role-specific dashboards
│   ├── registration/       # Login/register pages
│   └── partials/           # Reusable components
│
├── static/                  # CSS, JS, images
│   └── myapp/
│       ├── style.css       # Custom styles
│       └── logo.png        # App logo
│
├── .gitignore              # Git ignore rules
├── requirements.txt        # Python dependencies
├── manage.py               # Django management
└── README.md               # You are here!
```

---

## Database Schema

The system uses four main models that work together:

```
User (Django built-in)
  ↓
Profile → Stores role (admin/hr/employee)
  
Employee → Personal & job information
  ↓
SalaryStructure → Salary components (basic, HRA, allowances, deductions)
  ↓
Payroll → Monthly payment records (paid/unpaid status)
```

**Key Relationships:**
- One User has one Profile (1:1)
- One Employee has one SalaryStructure (1:1)
- One SalaryStructure has many Payrolls (1:N)

---

## API Routes

| Method | URL | View | Access |
|--------|-----|------|--------|
| GET | `/` | select_role | Public |
| GET/POST | `/login/admin/` | login_admin_view | Public |
| GET/POST | `/login/hr/` | login_hr_view | Public |
| GET/POST | `/login/emp/` | login_emp_view | Public |
| GET | `/logout/` | custom_logout | Authenticated |
| GET | `/dashboard/` | dashboard_view | Authenticated |
| GET/POST | `/register/` | register_emp_view | Public |
| GET/POST | `/register/hr/` | register_hr_view | Public |
| GET | `/employees/` | manage_employees | Admin, HR |
| GET/POST | `/employee/add/` | add_employee | Admin, HR |
| GET/POST | `/employee/<id>/edit/` | edit_employee | Admin, HR |
| POST | `/employee/<id>/delete/` | delete_employee | Admin |
| GET | `/my-salary/` | employee_salary_view | Employee |
| GET/POST | `/salary/update/` | update_salary_structure | Admin, HR |
| POST | `/payroll/mark-paid/<id>/` | mark_payroll_paid | Admin, HR |
| GET | `/preview-payslip/<id>/` | preview_payslip | Authenticated |

---

## Security Features

✅ **Role-based access control** - Custom decorator system  
✅ **CSRF protection** - On all forms  
✅ **Reserved usernames** - Prevents registration as 'admin', 'hr', etc.  
✅ **Password hashing** - Django's built-in password system  
✅ **SQL injection protection** - Django ORM handles queries safely  
✅ **XSS protection** - Django templates auto-escape output  

---

## Deployment

The app is deployed on PythonAnywhere. Here's the basic process I followed:

1. **Pushed code to GitHub**
   ```bash
   git add.
   git commit -m "Initial commit."
   git push origin main
   ```

2. **Cloned on PythonAnywhere**
   ```bash
   git clone https://github.com/YOUR-USERNAME/django-payroll-system.git
   ```

3. **Set up virtual environment**
   ```bash
   mkvirtualenv --python=/usr/bin/python3.10 myenv
   pip install -r requirements.txt
   ```

4. **Configured WSGI file** - Point to Django application

5. **Set up static files** - Configure static file paths

6. **Updated settings** - Add domain to `ALLOWED_HOSTS`

7. **Ran migrations & created superuser**

8. **Reloaded web app**

**Live at:** [https://manojgs.pythonanywhere.com](https://manojgs.pythonanywhere.com)

---

## Environment Variables

For production, use environment variables for sensitive data:

```python
# In settings.py
from decouple import config

SECRET_KEY = config('SECRET_KEY')
DEBUG = config('DEBUG', default=False, cast=bool)
```

Create a `.env` file (don't commit this!):
```
SECRET_KEY=your-secret-key-here
DEBUG=True
DATABASE_NAME=payroll_db
DATABASE_USER=root
DATABASE_PASSWORD=your-password
```

---

## Some Challenges I Faced

**1. Role-based routing:** Initially, I tried using Django's built-in permissions, but found it overly complex for just 3 roles. Creating custom decorators was much cleaner.

**2. Salary calculations:** Deciding whether to store the net salary or calculate it on-the-fly. I went with a calculation using `@property` to avoid data inconsistencies.

**3. Team collaboration:** Working with another developer meant being careful about merge conflicts and keeping our work synchronized. We used feature branches and daily check-ins.

**4. Deployment quirks:** PythonAnywhere has some specific requirements for static files and WSGI configuration that took a bit to figure out.

---

## What I Learned

- **Django's ORM is powerful:** Once you understand querysets and `select_related`, you can optimize database queries significantly
- **Custom decorators are your friend:** Sometimes the built-in solutions are overkill for your use case
- **Keep it simple:** I almost added a React frontend, but vanilla JavaScript was enough
- **Security matters:** Reserved usernames, CSRF protection, and role-based access aren't optional
- **Git saves lives:** Seriously, commit often and write clear commit messages
- **Documentation is crucial:** Good README and code comments make collaboration smooth

---

## Future Improvements

If I had more time, here's what I'd add:

- [ ] PDF generation for payslips using ReportLab
- [ ] Email notifications when payroll is processed
- [ ] Multi-currency support for international teams
- [ ] Attendance integration
- [ ] Leave management system
- [ ] Tax calculations based on salary slabs
- [ ] Bulk upload employees via CSV
- [ ] REST API for mobile app integration
- [ ] Two-factor authentication
- [ ] Export reports to Excel/CSV
- [ ] Dark mode toggle
- [ ] Performance dashboard with analytics

---

## Contributing

This was built as a learning project, but if you spot any bugs or have suggestions for improvements, feel free to:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## Testing

Run tests with:
```bash
python manage.py test
```

*(Note: Test suite is a work in progress)*

---

## Acknowledgments

Built this as a collaborative project with my teammate. We divided the work by technical layers:
- **Person 1:** Backend architecture, authentication system, database design, employee management
- **Person 2:** Payroll logic, salary calculations, frontend dashboards, Chart.js integration

It was a great learning experience in team development and Git workflows!

**Special thanks to:**
- Django documentation for being thorough
- Bootstrap team for the amazing CSS framework
- Chart.js for making data visualization simple
- PythonAnywhere for free hosting
- Stack Overflow community for troubleshooting help

---

## License

This project is open source and available for educational purposes. Feel free to fork it and build something cool!

MIT License - see [LICENSE](LICENSE) file for details.

---

## Contact & Links

- **Live Demo:** [https://manojgs.pythonanywhere.com](https://manojgs.pythonanywhere.com)
- **Report Bug:** [Open an issue](https://github.com/YOUR-USERNAME/django-payroll-system/issues)
- **Request Feature:** [Open an issue](https://github.com/YOUR-USERNAME/django-payroll-system/issues)

---

## Support

If you found this project helpful, consider:
- ⭐ Starring the repository
- 🐛 Reporting bugs
- 📝 Improving documentation
- 🔀 Submitting pull requests

---

**Built with ❤️ using Django and way too much coffee ☕**

---

### Project Status: ✅ Active

Last updated: March 2026
