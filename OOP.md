class Student:
    def __init__(self, name, surname, gender):
        self.name = name
        self.surname = surname
        self.gender = gender
        self.finished_courses = []
        self.courses_in_progress = []
        self.grades = {}
    
    def rate_lecturer(self, lecturer, course, grade):
        if isinstance(lecturer, Lecturer) and course in lecturer.courses_attached \
                and course in self.courses_in_progress:
            if course in lecturer.grades:
                lecturer.grades[course].append(grade)
            else:
                lecturer.grades[course] = [grade]
        else:
            return 'Ошибка'

    def average_grade(self):
        total_sum = sum(sum(grades) for grades in self.grades.values())
        total_count = sum(len(grades) for grades in self.grades.values())
        return total_sum / total_count if total_count else 0

    def __str__(self):
        avg_grade = self.average_grade()
        return (f'Имя: {self.name}\n'
                f'Фамилия: {self.surname}\n'
                f'Средняя оценка за домашние задания: {avg_grade:.2f}\n'
                f'Курсы в процессе изучения: {", ".join(self.courses_in_progress)}\n'
                f'Завершенные курсы: {", ".join(self.finished_courses)}')

    def __lt__(self, other):
        if isinstance(other, Student):
            return self.average_grade() < other.average_grade()
        return NotImplemented
        
class Mentor:
    def __init__(self, name, surname):
        self.name = name
        self.surname = surname
        self.courses_attached = []
        
    def rate_hw(self, student, course, grade):
        if isinstance(student, Student) and course in self.courses_attached and course in student.courses_in_progress:
            if course in student.grades:
                student.grades[course].append(grade)
            else:
                student.grades[course] = [grade]
        else:
            return 'Ошибка'

class Lecturer(Mentor):
    def __init__(self, name, surname):
        super().__init__(name, surname)
        self.grades = {}

    def average_grade(self):
        total_sum = sum(sum(grades) for grades in self.grades.values())
        total_count = sum(len(grades) for grades in self.grades.values())
        return total_sum / total_count if total_count else 0

    def __str__(self):
        avg_grade = self.average_grade()
        return (f'Имя: {self.name}\n'
                f'Фамилия: {self.surname}\n'
                f'Средняя оценка за лекции: {avg_grade:.2f}')

    def __lt__(self, other):
        if isinstance(other, Lecturer):
            return self.average_grade() < other.average_grade()
        return NotImplemented

class Reviewer(Mentor):
    def __init__(self, name, surname):
        super().__init__(name, surname)

    def review_hw(self, student: Student, course: str, grade: int):
        self.rate_hw(student, course, grade)

    def __str__(self):
        return f'Имя: {self.name}\nФамилия: {self.surname}'

# Функции для подсчета средней оценки
def average_student_grade(students, course):
    total_sum = 0
    total_count = 0
    for student in students:
        if course in student.grades:
            total_sum += sum(student.grades[course])
            total_count += len(student.grades[course])
    return total_sum / total_count if total_count else 0


def average_lecturer_grade(lecturers, course):
    total_sum = 0
    total_count = 0
    for lecturer in lecturers:
        if course in lecturer.grades:
            total_sum += sum(lecturer.grades[course])
            total_count += len(lecturer.grades[course])
    return total_sum / total_count if total_count else 0


# Создание экземпляров классов
student1 = Student('Ruoy', 'Eman', 'male')
student1.courses_in_progress += ['Python']
student1.finished_courses += ['Введение в программирование']

student2 = Student('Anna', 'Smith', 'female')
student2.courses_in_progress += ['Python']
student2.finished_courses += ['Математика']

lecturer1 = Lecturer('John', 'Doe')
lecturer1.courses_attached += ['Python']

lecturer2 = Lecturer('Alice', 'Wonderland')
lecturer2.courses_attached += ['Python']

reviewer1 = Reviewer('Jane', 'Smith')
reviewer1.courses_attached += ['Python']

reviewer2 = Reviewer('Bob', 'Brown')
reviewer2.courses_attached += ['Python']

# Выставление оценок
reviewer1.review_hw(student1, 'Python', 9)
reviewer1.review_hw(student2, 'Python', 8)

student1.rate_lecturer(lecturer1, 'Python', 10)
student1.rate_lecturer(lecturer2, 'Python', 9)

student2.rate_lecturer(lecturer1, 'Python', 8)
student2.rate_lecturer(lecturer2, 'Python', 9)

# Печать информации обо всех экземплярах
print(student1)
print(student2)
print(lecturer1)
print(lecturer2)
print(reviewer1)
print(reviewer2)

# Подсчет средних оценок
students = [student1, student2]
lecturers = [lecturer1, lecturer2]

average_student = average_student_grade(students, 'Python')
average_lecturer = average_lecturer_grade(lecturers, 'Python')

print(f'\nСредняя оценка за домашние задания по курсу Python: {average_student:.2f}')
print(f'Средняя оценка за лекции по курсу Python: {average_lecturer:.2f}')

