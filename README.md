import java.io.*;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.util.Scanner;

public class DiaryApp {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        String[] entries = new String[100];
        LocalDateTime[] dates = new LocalDateTime[100];
        int count = 0;
        String dateFormatPattern = "yyyy-MM-dd HH:mm";
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern(dateFormatPattern);

        System.out.println("Вы хотите загрузить существующий дневник?");
        String answer = scanner.nextLine();

        if (answer.equalsIgnoreCase("да")) {
            System.out.println("Введите путь к файлу для загрузки:");
            String path = scanner.nextLine();
            try {
                BufferedReader reader = new BufferedReader(new FileReader(path));
                String line;
                while ((line = reader.readLine()) != null) {
                    if (line.isEmpty()) continue;
                    LocalDateTime date = LocalDateTime.parse(line, formatter);
                    String text = reader.readLine();
                    dates[count] = date;
                    entries[count] = text;
                    count++;
                }
                reader.close();
                System.out.println("Дневник успешно загружен.");
            } catch (Exception e) {
                System.out.println("Ошибка при загрузке дневника: " + e.getMessage());
            }
        }

        System.out.println("Введите желаемый формат даты и времени:");
        String customFormat = scanner.nextLine();
        if (!customFormat.isEmpty()) {
            try {
                formatter = DateTimeFormatter.ofPattern(customFormat);
            } catch (Exception e) {
                System.out.println("Неверный формат, используется стандартный.");
                formatter = DateTimeFormatter.ofPattern(dateFormatPattern);
            }
        }

        boolean running = true;
        while (running) {
            System.out.println("1. Добавить запись");
            System.out.println("2. Просмотреть записи");
            System.out.println("3. Выход");
            String option = scanner.nextLine();

            if (option.equals("1")) {
                if (count >= entries.length) {
                    System.out.println("Дневник заполнен.");
                    continue;
                }
                System.out.println("Введите текст записи:");
                String text = scanner.nextLine();
                entries[count] = text;
                dates[count] = LocalDateTime.now();
                count++;
            } else if (option.equals("2")) {
                for (int i = 0; i < count; i++) {
                    System.out.println(dates[i].format(formatter));
                    System.out.println(entries[i]);
                    System.out.println();
                }
            } else if (option.equals("3")) {
                running = false;
            } else {
                System.out.println("Неверная опция.");
            }
        }

        System.out.println("Вы хотите сохранить дневник? (да/нет)");
        String saveAnswer = scanner.nextLine();
        if (saveAnswer.equalsIgnoreCase("да")) {
            System.out.println("Введите путь к файлу для сохранения:");
            String savePath = scanner.nextLine();
            try {
                BufferedWriter writer = new BufferedWriter(new FileWriter(savePath));
                for (int i = 0; i < count; i++) {
                    writer.write(dates[i].format(formatter));
                    writer.newLine();
                    writer.write(entries[i]);
                    writer.newLine();
                    writer.newLine();
                }
                writer.close();
                System.out.println("Дневник успешно сохранён.");
            } catch (Exception e) {
                System.out.println("Ошибка при сохранении дневника: " + e.getMessage());
            }
        }

    }
}
