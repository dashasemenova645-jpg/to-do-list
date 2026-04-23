# to-do list
import javax.swing.*;
import javax.swing.border.*;
import java.awt.*;
import java.awt.event.*;
import java.io.*;
import java.util.*;

public class TodoListGUI {
    
    // Компоненты окна
    private JFrame frame;
    private DefaultListModel<String> listModel;
    private JList<String> taskList;
    private JTextField taskInput;
    private JLabel statusLabel;
    
    public TodoListGUI() {
        createAndShowGUI();
    }
    
    private void createAndShowGUI() {
        // Создаём главное окно
        frame = new JFrame("📝 Мой To-Do List");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(500, 550);
        frame.setLocationRelativeTo(null); // По центру экрана
        
        // Создаём панель с отступами
        JPanel mainPanel = new JPanel(new BorderLayout(10, 10));
        mainPanel.setBorder(new EmptyBorder(15, 15, 15, 15));
        mainPanel.setBackground(new Color(245, 245, 250));
        
        // ========== ВЕРХНЯЯ ЧАСТЬ (заголовок) ==========
        JLabel titleLabel = new JLabel("📋 Список дел", JLabel.CENTER);
        titleLabel.setFont(new Font("Segoe UI", Font.BOLD, 24));
        titleLabel.setForeground(new Color(70, 70, 100));
        mainPanel.add(titleLabel, BorderLayout.NORTH);
        
        // ========== ЦЕНТР (список задач) ==========
        listModel = new DefaultListModel<>();
        taskList = new JList<>(listModel);
        taskList.setFont(new Font("Segoe UI", Font.PLAIN, 16));
        taskList.setBackground(Color.WHITE);
        taskList.setSelectionBackground(new Color(200, 220, 255));
        
        JScrollPane scrollPane = new JScrollPane(taskList);
        scrollPane.setBorder(BorderFactory.createTitledBorder("✨ Задачи"));
        scrollPane.setPreferredSize(new Dimension(450, 300));
        mainPanel.add(scrollPane, BorderLayout.CENTER);
        
        // ========== НИЖНЯЯ ЧАСТЬ (ввод и кнопки) ==========
        JPanel bottomPanel = new JPanel(new BorderLayout(10, 10));
        bottomPanel.setBackground(new Color(245, 245, 250));
        
        // Поле для ввода
        taskInput = new JTextField();
        taskInput.setFont(new Font("Segoe UI", Font.PLAIN, 14));
        taskInput.setPreferredSize(new Dimension(300, 35));
        
        // Панель с кнопками
        JPanel buttonPanel = new JPanel(new FlowLayout(FlowLayout.CENTER, 10, 5));
        
        JButton addButton = createStyledButton("➕ Добавить", new Color(60, 180, 100));
        JButton doneButton = createStyledButton("✅ Выполнено", new Color(70, 130, 200));
        JButton deleteButton = createStyledButton("🗑️ Удалить", new Color(220, 80, 70));
        JButton saveButton = createStyledButton("💾 Сохранить", new Color(255, 170, 50));
        JButton loadButton = createStyledButton("📂 Загрузить", new Color(100, 100, 200));
        
        buttonPanel.add(addButton);
        buttonPanel.add(doneButton);
        buttonPanel.add(deleteButton);
        buttonPanel.add(saveButton);
        buttonPanel.add(loadButton);
        
        // Статусная строка
        statusLabel = new JLabel("✅ Готов к работе");
        statusLabel.setFont(new Font("Segoe UI", Font.ITALIC, 12));
        statusLabel.setForeground(new Color(100, 100, 100));
        
        bottomPanel.add(taskInput, BorderLayout.NORTH);
        bottomPanel.add(buttonPanel, BorderLayout.CENTER);
        bottomPanel.add(statusLabel, BorderLayout.SOUTH);
        
        mainPanel.add(bottomPanel, BorderLayout.SOUTH);
        
        // ========== ДЕЙСТВИЯ КНОПОК ==========
        
        // Добавить задачу (по кнопке или по Enter)
        addButton.addActionListener(e -> addTask());
        taskInput.addActionListener(e -> addTask());
        
        // Отметить выполненную
        doneButton.addActionListener(e -> markAsDone());
        
        // Удалить задачу
        deleteButton.addActionListener(e -> deleteTask());
        
        // Сохранить в файл
        saveButton.addActionListener(e -> saveTasks());
        
        // Загрузить из файла
        loadButton.addActionListener(e -> loadTasks());


// Двойной клик для отметки о выполнении
        taskList.addMouseListener(new MouseAdapter() {
            public void mouseClicked(MouseEvent e) {
                if (e.getClickCount() == 2) {
                    markAsDone();
                }
            }
        });
        
        frame.add(mainPanel);
        frame.setVisible(true);
        
        // Автоматически загружаем сохранённые задачи
        loadTasks();
    }
    
    // Создаём стильные кнопки
    private JButton createStyledButton(String text, Color color) {
        JButton button = new JButton(text);
        button.setBackground(color);
        button.setForeground(Color.WHITE);
        button.setFont(new Font("Segoe UI", Font.BOLD, 13));
        button.setFocusPainted(false);
        button.setBorder(BorderFactory.createEmptyBorder(8, 15, 8, 15));
        button.setCursor(new Cursor(Cursor.HAND_CURSOR));
        
        // Эффект при наведении
        button.addMouseListener(new MouseAdapter() {
            public void mouseEntered(MouseEvent e) {
                button.setBackground(color.darker());
            }
            public void mouseExited(MouseEvent e) {
                button.setBackground(color);
            }
        });
        return button;
    }
    
    // Добавление задачи
    private void addTask() {
        String task = taskInput.getText().trim();
        if (!task.isEmpty()) {
            listModel.addElement("⏳ " + task);
            taskInput.setText("");
            statusLabel.setText("✅ Добавлена: " + task);
        } else {
            statusLabel.setText("⚠️ Введи задачу!");
        }
    }
    
    // Отметка о выполнении
    private void markAsDone() {
        int selected = taskList.getSelectedIndex();
        if (selected != -1) {
            String task = listModel.get(selected);
            if (task.startsWith("⏳")) {
                listModel.set(selected, task.replace("⏳", "✅"));
                statusLabel.setText("🎉 Поздравляю! Задача выполнена!");
            } else {
                statusLabel.setText("ℹ️ Эта задача уже выполнена");
            }
        } else {
            statusLabel.setText("⚠️ Выбери задачу!");
        }
    }
    
    // Удаление задачи
    private void deleteTask() {
        int selected = taskList.getSelectedIndex();
        if (selected != -1) {
            String task = listModel.get(selected);
            listModel.remove(selected);
            statusLabel.setText("🗑️ Удалена: " + task.substring(3));
        } else {
            statusLabel.setText("⚠️ Выбери задачу для удаления!");
        }
    }
    
    // Сохранение в файл
    private void saveTasks() {
        try (PrintWriter writer = new PrintWriter(new FileWriter("tasks.txt"))) {
            for (int i = 0; i < listModel.size(); i++) {
                writer.println(listModel.get(i));
            }
            statusLabel.setText("💾 Задачи сохранены!");
        } catch (IOException e) {
            statusLabel.setText("❌ Ошибка сохранения!");
        }
    }
    
    // Загрузка из файла
    private void loadTasks() {
        File file = new File("tasks.txt");
        if (file.exists()) {
            try (Scanner scanner = new Scanner(file)) {
                listModel.clear();
                while (scanner.hasNextLine()) {
                    listModel.addElement(scanner.nextLine());
                }
                statusLabel.setText("📂 Загружено " + listModel.size() + " задач");
            } catch (IOException e) {
                statusLabel.setText("⚠️ Не удалось загрузить");
            }
        }
    }
    
    // Запуск приложения
    public static void main(String[] args) {
        // Запускаем в специальном потоке (правила Java для окон)
        SwingUtilities.invokeLater(() -> new TodoListGUI());
    }
}
