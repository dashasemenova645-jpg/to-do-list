# to-do list
import java.util.ArrayList;
import java.util.Scanner;

/**
 * Простой консольный ToDo-лист на Java.
 * Позволяет добавлять, удалять, просматривать и отмечать задачи.
 */
public class TodoList {
    
    // Хранилище задач: список строк
    private static ArrayList<String> tasks = new ArrayList<>();
    
    // Массив для отметки "выполнено" (true/false)
    private static ArrayList<Boolean> status = new ArrayList<>();
    
    // Сканер для ввода с клавиатуры
    private static Scanner scanner = new Scanner(System.in);
    
    public static void main(String[] args) {
        System.out.println("Добро пожаловать в ToDo-лист!");
        
        // Бесконечный цикл меню
        while (true) {
            printMenu();      // показать меню
            int choice = getChoice();  // получить выбор пользователя
            
            switch (choice) {
                case 1:
                    addTask();        // добавить задачу
                    break;
                case 2:
                    deleteTask();     // удалить задачу
                    break;
                case 3:
                    showTasks();      // показать все задачи
                    break;
                case 4:
                    markTaskDone();   // отметить задачу выполненной
                    break;
                case 5:
                    System.out.println("Выход из программы. До свидания!");
                    scanner.close();
                    return;           // завершить программу
                default:
                    System.out.println("Неверный выбор. Попробуйте снова.");
            }
        }
    }
    
    /**
     * Выводит меню на экран.
     */
    private static void printMenu() {
        System.out.println("\n=== МЕНЮ ===");
        System.out.println("1. Добавить задачу");
        System.out.println("2. Удалить задачу");
        System.out.println("3. Показать все задачи");
        System.out.println("4. Отметить задачу как выполненную");
        System.out.println("5. Выход");
        System.out.print("Ваш выбор: ");
    }
    
    /**
     * Считывает число, введённое пользователем.
     */
    private static int getChoice() {
        while (!scanner.hasNextInt()) {
            System.out.print("Введите число от 1 до 5: ");
            scanner.next(); // очищаем неправильный ввод
        }
        return scanner.nextInt();
    }
    
    /**
     * Добавляет новую задачу.
     */
    private static void addTask() {
        System.out.print("Введите описание задачи: ");
        scanner.nextLine(); // очистка буфера после nextInt()
        String task = scanner.nextLine();
        
        if (!task.trim().isEmpty()) {
            tasks.add(task);
            status.add(false); // по умолчанию задача не выполнена
            System.out.println("✓ Задача добавлена!");
        } else {
            System.out.println("Ошибка: задача не может быть пустой.");
        }
    }
    
    /**
     * Удаляет задачу по номеру.
     */
    private static void deleteTask() {
        if (tasks.isEmpty()) {
            System.out.println("Список задач пуст. Нечего удалять.");
            return;
        }
        
        showTasks(); // показываем список для удобства
        System.out.print("Введите номер задачи для удаления: ");
        int num = getNumberInput();
        
        if (num >= 1 && num <= tasks.size()) {
            String removed = tasks.remove(num - 1);
            status.remove(num - 1);
            System.out.println("✓ Задача \"" + removed + "\" удалена.");
        } else {
            System.out.println("Ошибка: неверный номер задачи.");
        }
    }
    
    /**
     * Показывает все задачи с их статусом и номерами.
     */
    private static void showTasks() {
        if (tasks.isEmpty()) {
            System.out.println("Список задач пуст. Добавьте что-нибудь :)");
            return;
        }
        
        System.out.println("\n=== ВАШИ ЗАДАЧИ ===");
        for (int i = 0; i < tasks.size(); i++) {
            String marker = status.get(i) ? "[✓]" : "[ ]";
            System.out.println((i + 1) + ". " + marker + " " + tasks.get(i));
        }
    }
    
    /**
     * Отмечает задачу как выполненную.
     */
    private static void markTaskDone() {
        if (tasks.isEmpty()) {
            System.out.println("Нет задач, которые можно отметить.");
            return;
        }
        
        showTasks();
        System.out.print("Введите номер задачи, которую выполнили: ");
        int num = getNumberInput();
        
        if (num >= 1 && num <= tasks.size()) {
            status.set(num - 1, true);
            System.out.println("✓ Задача \"" + tasks.get(num - 1) + "\" отмечена как выполненная!");
        } else {
            System.out.println("Ошибка: неверный номер.");
        }
    }
    
    /**
     * Безопасно получает целое число из ввода пользователя.
     */
    private static int getNumberInput() {
        while (!scanner.hasNextInt()) {
            System.out.print("Введите корректное число: ");
            scanner.next();
        }
        int num = scanner.nextInt();
        scanner.nextLine(); // очистка буфера
        return num;
    }
}
