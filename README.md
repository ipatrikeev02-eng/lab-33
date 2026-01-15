## Отчет по лабораторной работе № 3

#### № группы: `ПМ-2501`

#### Выполнила: `Патрикеев Иван Дмитриевич`

#### Вариант: `14`

### Cодержание:

- [Постановка задачи](#1-постановка-задачи)
- [Программа](#2-программа)
- [Тестирование программы](#3-анализ-правильности-решения) 
  
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
static class PoemGit {
    private String nazvanie;
    private String author;
    private int maxVer;
    private String[] curVer;
    private int verCount;
    private String[][] verHistory;

    public PoemGit(String nazvanie, String author, int maxVer) {
        this.nazvanie = nazvanie;
        this.author = author;
        this.maxVer = maxVer;
        this.curVer = new String[0];
        this.verCount = 0;
        this.verHistory = new String[maxVer][];

    }

    @Override
    public String toString() {
        StringBuilder s = new StringBuilder();
        s.append(nazvanie).append(" - ").append(author).append("\n");
        for (int i = 0; i < curVer.length; i++) {
            s.append(i + 1).append(". ").append(curVer[i]).append("\n");
        }
        return s.toString();
    }

    public void addLine(String line) {
        String[] newLine = new String[curVer.length + 1];
        for (int i = 0; i < curVer.length; i++) {
            newLine[i] = curVer[i];
        }
        newLine[curVer.length] = line;
        curVer = newLine;
    }

    public void deleteLine(int num) {
        if (num < 1 || num > curVer.length) {
            return;
        }
        String[] newLine = new String[curVer.length - 1];
        int newIndex = 0;
        for (int i = 0; i < curVer.length; i++) {
            if (i != num - 1) {
                newLine[newIndex++] = curVer[i];
            }
        }
        curVer = newLine;
    }

    public void insertLine(int num, String line) {
        if (num < 1 || num > curVer.length + 1) {
            return;
        }
        String[] newLine = new String[curVer.length + 1];
        int newIndex = 0;
        for (int i = 0; i < curVer.length; i++) {
            if (i == num - 1) {
                newLine[newIndex++] = line;
            }
            newLine[newIndex++] = curVer[i];
        }
        if (num == curVer.length + 1) {
            newLine[newIndex] = line;
        }
        curVer = newLine;
    }

    public void saveVer() {
        String[] copy = new String[curVer.length];
        for (int i = 0; i < curVer.length; i++) {
            copy[i] = curVer[i];
        }
        if (verCount < maxVer) {
            verHistory[verCount] = copy;
            verCount++;
        } else {
            for (int i = 0; i < maxVer - 1; i++) {
                verHistory[i] = verHistory[i + 1];
            }
            verHistory[maxVer - 1] = copy;
        }
    }

    public void showSavedVersions() {
        if (verCount == 0) {
            System.out.println("Нет сохраненных версий");
            return;
        }
        System.out.println("Сохраненные версии:");
        for (int i = 0; i < verCount; i++) {
            System.out.println((i + 1) + ". ");
            String[] line = verHistory[i];
            for (int j = 0; j < line.length; j++) {
                System.out.println("   " + (j + 1) + "." + line[j]);
            }
        }
    }

    public void deleteSavedVersion(int num) {
        if (num < 1 || num > verCount) {
            return;
        }
        for (int i = num - 1; i < verCount - 1; i++) {
            verHistory[i] = verHistory[i + 1];
        }
        verHistory[verCount - 1] = null;
        verCount--;
    }

    public void revertToVersion(int num) {
        if (num < 1 || num > verCount) {
            return;
        }
        String[] w = verHistory[num - 1];
        curVer = new String[w.length];
        for (int i = 0; i < w.length; i++) {
            curVer[i] = w[i];
        }
        for (int i = num - 1; i < verCount - 1; i++) {
            verHistory[i] = verHistory[i + 1];
        }
        verHistory[verCount - 1] = null;
        verCount = num - 1;
    }

    private String[] getVersion(int ver) {
        if (ver == 0) {
            return curVer;
        } else if (ver >= 1 && ver <= verCount) {
            return verHistory[ver - 1];
        }
        return null;
    }

    private void printLine(String[] line) {
        for (int i = 0; i < line.length; i++) {
            System.out.println((i + 1) + ". " + line[i]);
        }
    }

    public void compareVersions(int ver1, int ver2) {
        String[] v1 = getVersion(ver1);
        String[] v2 = getVersion(ver2);
        if (v1 == null || v2 == null) {
            System.out.println("Ошибка");
            return;
        }
        System.out.println("Сравнение версий:");
        System.out.println("Версия" + ver1 + ":");
        printLine(v1);
        System.out.println("Версия" + ver2 + ":");
        printLine(v2);
    }

    private boolean containsLine(String[] w, String v) {
        for (int i = 0; i < w.length; i++) {
            if (w[i].equals(v)) {
                return true;
            }
        }
        return false;
    }

    public void showCommonLines(int ver1, int ver2) {
        String[] v1 = getVersion(ver1);
        String[] v2 = getVersion(ver2);
        if (v1 == null || v2 == null) {
            System.out.println("Ошибка");
            return;
        }
        String[] newLine;
        String[] oldLine;
        if (ver1 > ver2) {
            newLine = v1;
            oldLine = v2;
        } else {
            newLine = v2;
            oldLine = v1;
        }
        System.out.println("Общие строки:");
        for (int i = 0; i < newLine.length; i++) {
            String curLine = newLine[i];
            boolean found = false;
            for (int j = 0; j < oldLine.length; j++) {
                if (oldLine[j].equals(curLine)) {
                    found = true;
                    break;
                }
            }
            if (found) {
                System.out.println(curLine);
            }
        }
    }

    public void showDifferentLines(int v1, int v2) {
        String[] l1 = getVersion(v1);
        String[] l2 = getVersion(v2);
        if (l1 == null || l2 == null) {
            System.out.println("Ошибка");
            return;
        }
        System.out.println("Строки, которые есть только в одной версии:");
        for (int i = 0; i < l1.length; i++) {
            String line = l1[i];
            boolean w = false;
            for (int j = 0; j < l2.length; j++) {
                if (l2[j].equals(line)) {
                    w = true;
                    break;
                }
            }
            if (!w) {
                System.out.println("- " + line);
            }
        }
        for (int i = 0; i < l2.length; i++) {
            String line = l2[i];
            boolean w = false;
            for (int j = 0; j < l1.length; j++) {
                if (l1[j].equals(line)) {
                    w = true;
                    break;
                }
            }
            if (!w) {
                System.out.println("+ " + line);
            }
        }
    }
}
```
