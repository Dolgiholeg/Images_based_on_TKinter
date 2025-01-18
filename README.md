# Программа для создания изображений на основе TKinter
## Задача №2. Реализовать функционал: Инструмент "Ластик"

  import tkinter as tk
  
  from tkinter import colorchooser, filedialog, messagebox
  
  from PIL import Image, ImageDraw
  
  
  """
  Данная программа представляет собой пример использования библиотеки Tkinter для создания графического интерфейса.
  Обработка событий
  - : Событие привязано к методу paint, позволяя рисовать на холсте при перемещении мыши с нажатой левой кнопкой.
  - : Событие привязано к методу reset, который сбрасывает состояние рисования для начала новой линии.
  Использование приложения
  Пользователь может рисовать на холсте, выбирать цвет и размер кисти, очищать холст и сохранять в формате PNG.
  """
  
  
    class DrawingApp:
  
    """
    Инициализация __init__(self, root)
    Конструктор класса принимает один параметр:
    - root: Это корневой виджет Tkinter, который служит контейнером для всего интерфейса приложения.
    Внутри конструктора выполняются следующие ключевые действия:
    - Устанавливается заголовок окна приложения.
    - Создается объект изображения (self.image) с использованием библиотеки Pillow. Это изображение служит виртуальным
    холстом, на котором происходит рисование. Изначально оно заполнено белым цветом.
    - Инициализируется объект ImageDraw. Draw(self.image), который позволяет рисовать на объекте изображения.
    - Создается виджет Canvas Tkinter, который отображает графический интерфейс для рисования. Размеры холста
    установлены в 600x400 пикселей.
    - Вызывается метод self.setup_ui(), который настраивает элементы управления интерфейса.
    - Привязываются обработчики событий к холсту для отслеживания движений мыши при рисовании () и сброса состояния
    кисти при отпускании кнопки мыши ().
    """
    
      def __init__(self, root):
        self.root = root  # в классе создаётся переменная уровня класса root и инициализируется её с помощью объекта root
        self.root.title("Рисуем с сохранением в PNG")  # название главного окна будет «Рисунок с сохранением в PNG»

        self.image = Image.new("RGB", (1000, 600), "white")  # создаёт новое изображение в режиме RGB
        # с размерами 600 на 400 пикселей и белым фоном, это происходит с помощью метода Image.new() из библиотеки
        # Python Pillow (PIL)
        self.draw = ImageDraw.Draw(self.image)  # создание объекта для рисования на объекте изображения с использованием
        # библиотеки Pillow, этот объект позволяет добавлять на изображение текст, линии и другие графические элементы

        self.canvas = tk.Canvas(root, width=1000, height=600, bg='white')  # создаётся новый объект прямоугольное полотно с шириной
        # 600 пикселей, высотой 400 пикселей и белым фоном, это означает, что на созданной области можно будет рисовать
        # фигуры, создавать текст и размещать изображения
        self.canvas.pack()  # размещение виджета Canvas, это делается, потому что на следующем шаге будут выполняться
        # вычисления размеров полотна, и до тех пор, пока geometry manager не разместит виджет, у него не будет реальных
        # значений высоты и ширины

        self.setup_ui()  # метод, который используется для инициализации дизайна графического интерфейса

        self.last_x, self.last_y = None, None  # переменные last_x и last_y инициализируются значением None,
        # эти переменные хранят координаты последней нарисованной точки
        self.pen_color = 'black'  # атрибут - цвет кисти для рисования, изначально устанавливается на чёрный
        self.brush_size = 1  # атрибут - размер кисти для рисования, изначально устанавливается как 1
        self.previous_color = None  # атрибут - предыдущий цвет кисти для рисования, изначально не определён

        self.canvas.bind('<B1-Motion>', self.paint)  # при движении зажатой левой кнопки мыши будет вызываться метод paint
        self.canvas.bind('<ButtonRelease-1>', self.reset)  # при освобождении кнопки мыши будет вызываться функция reset.

      def setup_ui(self):
        """
        Этот метод отвечает за создание и расположение виджетов управления:
        - Кнопки "Очистить", "Выбрать цвет" и "Сохранить" позволяют пользователю очищать холст, выбирать цвет кисти и
        сохранять текущее изображение соответственно.
        - Выпадающее меню для изменения размера кисти дает возможность выбирать толщину линии от 1, 2, 5, 10 пикселей.
        """

        control_frame = tk.Frame(self.root)  # создание кадра (фрейма) в Tkinter на основе корневого окна (self.root),
        # позволяет создать рамку для размещения кнопок управления
        control_frame.pack(fill=tk.X)  # рамка control_frame будет расширяться горизонтально, заполняя любое
        # дополнительное пространство по оси x

        clear_button = tk.Button(control_frame, text="Очистить", command=self.clear_canvas)  # создаётся кнопка очистки
        # холста, при нажатии на неё будет вызываться функция clear_canvas, которая очищает холст с помощью метода
        # self.canvas.delete("all")
        clear_button.pack(side=tk.LEFT)  # кнопка clear_button будет прикреплена к левой стороне окна

        color_button = tk.Button(control_frame, text="Выбрать цвет", command=self.choose_color)
        color_button.pack(side=tk.LEFT)

        save_button = tk.Button(control_frame, text="Сохранить", command=self.save_image)
        save_button.pack(side=tk.LEFT)

        eraser_button = tk.Button(control_frame, text="Ластик", command=self.create_eraser)  # создаём кнопку "Ластик"
        eraser_button.pack(side=tk.LEFT)

        brush_button = tk.Button(control_frame, text="Кисть", command=self.create_brush)  # создаём кнопку "Кисть"
        brush_button.pack(side=tk.LEFT)

        # СОЗДАЁМ ВЫПАДАЮЩЕЕ МЕНЮ РАЗМЕРОВ КИСТИ
        sizes = [1, 2, 5, 10]  # создаём список значений размеров кисти
        label = tk.Label(control_frame, text=' Размер кисти:')  # создаём название
        label.pack(side=tk.LEFT)
        self.variable = tk.StringVar()  # задаём тип для исходной переменной размера кисти
        self.variable.set(str(sizes[0]))  # создаём значение размера кисти по умолчанию# размера кисти по умолчанию
        brush_size_menu = tk.OptionMenu(control_frame, self.variable,  *sizes, command=self.update_brush_size)
        # создаём выпадающее меню размеров кисти
        brush_size_menu.config(direction='above')  # выпадающий список будет выпадать вверх
        brush_size_menu.pack(side=tk.LEFT)

        # self.brush_size_scale = tk.Scale(control_frame, from_=1, to=10, orient=tk.HORIZONTAL)  # создаётся виджет
        # Scale (шкала) в библиотеке Tkinter
        # self.brush_size_scale.pack(side=tk.LEFT)  # виджет self.brush_size_scale будет выровнен по левой стороне контейнера

      def update_brush_size(self, size):
        """
        Обновление размера кисти.
        :param size: Новый размер кисти в виде строки.
        """
        self.brush_size = int(size)

      def create_eraser(self):
        """
        Создаём ластики в виде кисти с цветом полотна для рисования
        """
        self.pen_color = self.canvas['bg']

      def create_brush(self):
        """
        Создаём кисть
        Возврат к предыдущему цвету кисти.
        """
        self.pen_color = self.previous_color

      def paint(self, event):
        """
        Функция вызывается при движении мыши с нажатой левой кнопкой по холсту. Она рисует линии на холсте Tkinter и
        параллельно на объекте Image из Pillow:
        - event: Событие содержит координаты мыши, которые используются для рисования.
        - Линии рисуются между текущей и последней зафиксированной позициями курсора, что создает непрерывное изображение.
        """
        if self.last_x and self.last_y:  # проверка условий, связанных с переменными self.last_x и self.last_y, которые
            # хранят координаты последней нарисованной точки
            self.canvas.create_line(self.last_x, self.last_y, event.x, event.y,
                                    width=self.variable.get(), fill=self.pen_color,
                                    capstyle=tk.ROUND, smooth=tk.TRUE)
            # На холсте canvas в библиотеке Tkinter рисуется линия по заданным параметрам.
            # Параметры:
            # self.last_x и self.last_y — координаты начальной и конечной точек линии;
            # event.x и event.y — координаты события, по которому происходит рисование;
            # width — ширина линии (устанавливается с помощью выпадающего меню размеров кисти);
            # fill — цвет линии (устанавливается с помощью переменной self.pen_color);
            # cap style=tk.ROUND — стиль завершения линии (по умолчанию — butt, по умолчанию, если линия не имеет стрелки);
            # smooth=tk.TRUE — сглаживает сегменты линии (если значение «true» или «bezier»)
            self.draw.line([self.last_x, self.last_y, event.x, event.y], fill=self.pen_color, width=int(self.variable.get()))
            # на изображении будет нарисована линия с заданными параметрами

        self.last_x = event.x
        self.last_y = event.y
        # Фиксация расположения с сохранением координат события (x и y) для дальнейшего использования в приложении

        def reset(self, event):
        """
        Сбрасывает последние координаты кисти. Это необходимо для корректного начала новой линии после того, как
        пользователь отпустил кнопку мыши и снова начал рисовать.
        """
        self.last_x, self.last_y = None, None

      def clear_canvas(self):
        """
        Очищает холст, удаляя все нарисованное, и пересоздает объекты Image и ImageDraw для нового изображения.
        """
        self.canvas.delete("all")
        self.image = Image.new("RGB", (1000, 600), "white")
        self.draw = ImageDraw.Draw(self.image)

      def choose_color(self):
        """
        Открывает стандартное диалоговое окно выбора цвета и устанавливает выбранный цвет как текущий для кисти.
        """
        new_color = colorchooser.askcolor(color=self.pen_color)[1]  # сохраняем в переменную выбранный пользователем новый цвет кисти
        if new_color:  # если новый цвет кисти выбран
            self.pen_color = new_color  # атрибут цвет кисти равен новому цвету кисти выбранного пользователем
            self.previous_color = self.pen_color  # атрибут предыдущий цвет кисти равен атрибуту цвету кисти

      def save_image(self):
        """
        Позволяет пользователю сохранить изображение, используя стандартное диалоговое окно для сохранения файла.
        Поддерживает только формат PNG. В случае успешного сохранения выводится сообщение об успешном сохранении.
        """
        file_path = filedialog.asksaveasfilename(filetypes=[('PNG files', '*.png')])
        if file_path:
            if not file_path.endswith('.png'):
                file_path += '.png'
            self.image.save(file_path)
            messagebox.showinfo("Информация", "Изображение успешно сохранено!")


    def main():
    root = tk.Tk()
    app = DrawingApp(root)
    root.mainloop()


    if __name__ == "__main__":
    main()

![2025-01-18_08-19-45 Первоначальная работа кисти](https://github.com/user-attachments/assets/58a72011-f7af-4018-9d24-a5b7babb29b5)
![2025-01-18_08-21-23 Работа в режиме ластик](https://github.com/user-attachments/assets/d12ea783-f86e-4a15-8e06-0ed1176d5956)
![2025-01-18_08-22-23 Работа кисти после переключения из режима ластик](https://github.com/user-attachments/assets/fc2861e1-df76-4ee5-89c2-3e475e2c87de)

