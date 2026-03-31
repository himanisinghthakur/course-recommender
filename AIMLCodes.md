from rich.console import Console
from rich.spinner import Spinner
from rich.progress import Progress
from rich.panel import Panel
from rich.table import Table
from time import sleep
import math

console = Console()

console.print(Panel.fit(
    "[bold cyan]🤖 AI COURSE RECOMMENDATION SYSTEM 🤖[/bold cyan]\n"
    "[italic green]Welcome! Let's find the best courses for you...[/italic green]",
    border_style="bright_magenta"
))

data = [
    ### Coding
    ["coding", "beginner", "Python Basics"],
    ["coding", "intermediate", "Data Structures"],
    ["coding", "advanced", "Algorithms"],
    ["coding", "beginner", "Web Development Basics"],
    ["coding", "intermediate", "Backend Development"],
    ["coding", "advanced", "Full Stack Projects"],

    ### Design
    ["design", "beginner", "Graphic Design"],
    ["design", "intermediate", "UI/UX Design"],
    ["design", "advanced", "Animation"],
    ["design", "beginner", "Photoshop Essentials"],
    ["design", "intermediate", "Illustrator Skills"],
    ["design", "advanced", "3D Modeling"],

    ### Business
    ["business", "beginner", "Marketing Basics"],
    ["business", "intermediate", "Entrepreneurship"],
    ["business", "advanced", "Business Strategy"],
    ["business", "beginner", "Sales Techniques"],
    ["business", "intermediate", "Financial Management"],
    ["business", "advanced", "Corporate Leadership"],

    ### AI / Data Science
    ["ai", "beginner", "Introduction to AI"],
    ["ai", "intermediate", "Machine Learning Fundamentals"],
    ["ai", "advanced", "Deep Learning Projects"],
    ["ai", "beginner", "Data Analysis with Python"],
    ["ai", "intermediate", "Predictive Analytics"],
    ["ai", "advanced", "NLP & Computer Vision"],

    ### Digital Marketing
    ["marketing", "beginner", "Social Media Marketing"],
    ["marketing", "intermediate", "SEO & SEM"],
    ["marketing", "advanced", "Marketing Analytics"],
    ["marketing", "beginner", "Content Creation Basics"],
    ["marketing", "intermediate", "Email Campaigns"],
    ["marketing", "advanced", "Growth Hacking Strategies"]
]

interest_map = {
    "coding": 1, 
    "design": 2, 
    "business": 3,
    "ai": 4,
    "marketing": 5
}

level_map = {"beginner": 1, "intermediate": 2, "advanced": 3}

career_map = {
    ### Coding
    "Python Basics": "Start career in Software Development",
    "Data Structures": "Prepare for coding interviews",
    "Algorithms": "Advanced Software Engineering",
    "Web Development Basics": "Build websites",
    "Backend Development": "Become Backend Developer",
    "Full Stack Projects": "Work as Full Stack Developer",

    ### Design
    "Graphic Design": "Become a Designer",
    "UI/UX Design": "Work in Product Design",
    "Animation": "Enter Animation Industry",
    "Photoshop Essentials": "Start as Graphic Designer",
    "Illustrator Skills": "Create Illustrations Professionally",
    "3D Modeling": "Work in 3D Animation or Game Design",

    ### Business
    "Marketing Basics": "Start Digital Marketing Career",
    "Entrepreneurship": "Build your own startup",
    "Business Strategy": "Manage corporate strategy",
    "Sales Techniques": "Become Sales Professional",
    "Financial Management": "Work in Finance",
    "Corporate Leadership": "Lead Organizations",

    ### AI / Data Science
    "Introduction to AI": "Get started in AI field",
    "Machine Learning Fundamentals": "Work on ML projects",
    "Deep Learning Projects": "Advanced AI Research",
    "Data Analysis with Python": "Analyze business/data problems",
    "Predictive Analytics": "Build prediction models",
    "NLP & Computer Vision": "Work in AI applications",

    ### Digital Marketing
    "Social Media Marketing": "Grow brands online",
    "SEO & SEM": "Work in Search Marketing",
    "Marketing Analytics": "Analyze campaigns effectively",
    "Content Creation Basics": "Create engaging content",
    "Email Campaigns": "Run email marketing",
    "Growth Hacking Strategies": "Rapid business growth"
}

console.print("\n[bold yellow]Select your interest:[/bold yellow]")
console.print("1 → Coding\n2 → Design\n3 → Business\n4 → AI / Data Science\n5 → Marketing")
choice = input("Enter choice (1-5): ")
user_interest = { "1":"coding","2":"design","3":"business","4":"ai","5":"marketing"}.get(choice)
if not user_interest:
    console.print("[bold red]Invalid choice[/bold red]")
    exit()

console.print("\n[bold yellow]Select your learning stage:[/bold yellow]")
console.print("1 → Beginner\n2 → Intermediate\n3 → Advanced")
level_choice = input("Enter choice (1-3): ")
user_level = { "1":"beginner","2":"intermediate","3":"advanced"}.get(level_choice)
if not user_level:
    console.print("[bold red]Invalid choice[/bold red]")
    exit()

###    KNN LOGIC
user_point = [interest_map[user_interest], level_map[user_level]]
distances = []

for item in data:
    point = [interest_map[item[0]], level_map[item[1]]]
    distance = math.sqrt((user_point[0]-point[0])**2 + (user_point[1]-point[1])**2)
    distances.append((distance, item[2]))

distances.sort()
k = 3
top_k = distances[:k]

### Weighted Skill Boost
skill_scores = {}
for dist, course in top_k:
    weight = 1 / (dist+1)
    skill_scores[course] = skill_scores.get(course,0) + weight

recommended_course = max(skill_scores, key=skill_scores.get)
total_weight = sum(skill_scores.values())
skill_boost = (skill_scores[recommended_course]/total_weight)*100

###   ANIMATION SPINNER
spinner = Spinner("dots", text="Analyzing your profile using AI model...")
with console.status(spinner):
    sleep(3)

###    TOP COURSES TABLE
console.print("\n[bold magenta]🚀 🌟 TOP 3 COURSE RECOMMENDATIONS 🌟 🚀[/bold magenta]\n")
table = Table(title="Recommended Courses", title_style="bold cyan")
table.add_column("No.", style="yellow", justify="center")
table.add_column("Course Name", style="cyan", justify="center")
table.add_column("Recommendation", style="green", justify="center")

for i, (dist, course) in enumerate(top_k, start=1):
    if course == recommended_course:
        table.add_row(str(i), f"{course}", "⭐ Best Choice ⭐")
    else:
        table.add_row(str(i), f"{course}", "-")

console.print(table)

###    SKILL BOOST BAR
console.print("\n[bold yellow]Skill Boost:[/bold yellow]")
bar_length = 30
filled = int(skill_boost / (100/bar_length))
bar = "█"*filled + "-"*(bar_length-filled)
console.print(f"[bold magenta]{bar}[/bold magenta] {skill_boost:.2f}%")

console.print(f"🛠 [bold blue]Learning Stage:[/bold blue] {user_level.capitalize()}")
console.print(f"🎓 [bold magenta]Career Suggestion:[/bold magenta] {career_map.get(recommended_course)}")
console.print(f"💡 [bold green]Why this course fits you:[/bold green] Matches your interest & learning stage")

###   ENDING PANEL
console.print(Panel.fit(
    "[bold cyan]✨ Congratulations! Your personalized learning journey is ready ✨[/bold cyan]\n"
    "[bold green]💡 Keep progressing, explore knowledge, and unlock your potential! 🚀[/bold green]",
    border_style="bright_magenta"
))
