## Отчет по лабораторной работе № 3

#### № группы: `ПМ-2501`

#### Выполнила: `Патрикеев Иван Дмитриевич`

#### Вариант: `14`

### Cодержание:

- Постановка задачи
- Программа
- Тестирование программы 
  
### 1. Постановка задачи
1. Создание объекта гита
Создание объекта гита по названию стихотворения, автору и максимальному ко- личеству сохраняемых версий.
2. Вывод стихотворения (переопределение toString)
Отображение текущей версии стихотворения в формате: «НазваниеСтиха - Имя- Поэта» и строки построчно.
3. Добавление строки
Добавление новой строки в конец текущего стихотворения.
4. Удаление строки по номеру
Удаление строки с указанным номером (нумерация начинается с 1).
5. Вставка строки по номеру
Вставка новой строки в указанное место, сдвигая последующие строки вниз.
6. Сохранение версии
Сохранение текущей версии стихотворения. Если количество сохранений превы- шает максимум, самое старое сохранение удаляется. Обратите внимание: для кор- ректного сохранения массив строк копируется, а не передаётся по ссылке.
7. Просмотр сохранений
Вывод сохранённой версии. Нумерация версий: 0 — текущая версия, 1 — последнее сохранение, и так далее.
8. Удаление сохранения
Удаление указанного сохранения. Более ранние сохранения, если были удалены, не восстанавливаются.
9. Откат к сохранению
Откат текущей версии к указанному сохранению. При этом текущая версия и все более поздние сохранения стираются.
10. Сравнение версий
Сравнение двух версий или текущей версии с одной из сохранённых:
• Если передана одна версия, она сравнивается с текущей.
• Если переданы две версии, они сравниваются между собой.
11. Общие строки между версиями
Вывод строк, которые есть в обеих версиях, в порядке, в котором они встречаются в более новой версии.
12. Различные строки между версиями
Вывод строк, которые встречаются только в одной из версий:
• Строки из старой версии, которых нет в новой, помечаются знаком «-» и выводятся в порядке их появления в старой версии.
• Строки из новой версии, которых нет в старой, помечаются знаком «+» и выводятся в порядке их появления в новой версии.

### 2. Программа
```java
import java.util.*;
class PoemGit {
    private String title;
    private String author;
    private int maxVersion;
    private List<String> lines;
    private List<List<String>> history;
    public PoemGit(String title, String author, int maxVersion) {
        this.title = title;
        this.author = author;
        this.maxVersion = maxVersion;
        this.lines = new ArrayList<>();
        this.history = new ArrayList<>();
        history.add(new ArrayList<>(lines));

    } @Override
    public String toString() {
        StringBuilder sb = new StringBuilder();
        sb.append("«").append(title).append(" - ").append(author).append("»\n");
        for (String line : lines) {
            sb.append(line).append("\n");
        }
        return sb.toString();
    }

    public void addLine(String line) {
        lines.add(line);
    }

    public void deleteLine(int lineNumber) {
        if (lineNumber >= 1 && lineNumber <= lines.size()) {
            lines.remove(lineNumber - 1); // индексация с 0
        }
    }

    public void insertLine(int lineNumber, String line) {
        if (lineNumber >= 1 && lineNumber <= lines.size() + 1) {
            lines.add(lineNumber - 1, line);
        }
    }

    public void saveVersion() {
        List<String> currentCopy = new ArrayList<>(lines);
        history.add(0, currentCopy);
        if (history.size() > maxVersion + 1) {
            history.remove(history.size() - 1);
        }
    }

    public String viewVersion(int versionIndex) {
        if (versionIndex >= 0 && versionIndex < history.size()) {
            List<String> versionLines = history.get(versionIndex);
            StringBuilder sb = new StringBuilder();
            sb.append("«").append(title).append(" - ").append(author).append("» (Версия ").append(versionIndex).append(")\n");
            for (String line : versionLines) {
                sb.append(line).append("\n");
            }
            return sb.toString();
        } else {
            return "Ошибка: Неверный индекс версии.";
        }
    }

    public void deleteSavedVersion(int versionIndex) {
        if (versionIndex > 0 && versionIndex < history.size()) {
            history.remove(versionIndex);
        }
    }

    public void revertToVersion(int versionIndex) {
        if (versionIndex >= 0 && versionIndex < history.size()) {
            lines.clear();
            lines.addAll(history.get(versionIndex));
            history = history.subList(0, versionIndex + 1);
        }
    }

    public String compareVersions(int versionIndex1, int versionIndex2) {
        if (versionIndex1 < 0 || versionIndex1 >= history.size() ||
                versionIndex2 < 0 || versionIndex2 >= history.size()) {
            return "Ошибка: Неверный индекс версии.";
        }
        List<String> ver1 = history.get(versionIndex1);
        List<String> ver2 = history.get(versionIndex2);
        StringBuilder result = new StringBuilder();
        result.append("=== Общие строки между версиями ===\n");
        for (String line : ver1) {
            if (ver2.contains(line)) {
                result.append(line).append("\n");
            }
        }
        result.append("\n=== Различные строки между версиями ===\n");
        result.append("Строки из версии ").append(versionIndex1).append(", которых нет в версии ").append(versionIndex2).append(":\n");
        for (String line : ver1) {
            if (!ver2.contains(line)) {
                result.append("<- ").append(line).append("\n");
            }
        }
        result.append("Строки из версии ").append(versionIndex2).append(", которых нет в версии ").append(versionIndex1).append(":\n");
        for (String line : ver2) {
            if (!ver1.contains(line)) {
                result.append("-> ").append(line).append("\n");
            }
        }
        return result.toString();
    }
    public String compareVersions(int versionIndex) {
        return compareVersions(versionIndex, 0);
    }
}
```
