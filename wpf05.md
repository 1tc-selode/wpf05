# Grid: TicTacToe
## Feladat
Solution neve: **HangMan**

Projekt leírása: hozz létre egy 2x2-as Grid-et, aminek az bal felső cellájában ott lesz az Akasztófa cím, és ott lesz a kitalólondó szó helye. A bal alsó sarokban az választható betűk, abc található. A jobb felső sarkában található egy következő gomb, és egy textblock amiben kiírja hogy vesztett vagy nyert a játékos. A jobb alsó sarokban pedig maga az akasztófa fog megjelenni.



1. Window méretét beállítjuk 600x1200-asra
2. Grid: A Grid-et 2x2-esra osztjuk
3. Bal felső grid:
    - elosztjuk 2 sorra
    - az első sorban az Akasztófa cím található
    - a második sorban a kitalálandó szó kerül majd ide, ezt egy girdben adjuk meg, hogy később tudjunk rá hivatkozni és létrehozni benne buttonokat
4. Bal alsó grid:
    - itt 5x7-es felosztásban helyezzünk el 35 db buttonokat, ezeknek a contentje a magyar abc betűi lesznek (kivéve a kettős pl ny, ty és a hármas betűkkel dzs)
    - a Click eseményre a "Button_Click" eseménykezelőt állítjuk be minden Buttonra
5. Jobb felső grid:
    - elosztjuk 2 sorra
    - az első sorban kiírja ha a játékos nyert vagy veszített
    - a második sorban pedig egy következő buttont helyezzunk el, amivel egy új szavat generál
6. Jobb alsó grid:
    - Ide fog kerülni az akasztófa képe

```C#
<Window x:Class="HangMan.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:HangMan"
        mc:Ignorable="d"
        Title="Hangman" Height="600" Width="1200">
    <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition></ColumnDefinition>
            <ColumnDefinition></ColumnDefinition>
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition></RowDefinition>
            <RowDefinition></RowDefinition>
        </Grid.RowDefinitions>

        //Bal felső grid
        <Grid x:Name="mainGrid" Grid.Row="0" Grid.Column="0">
            <Grid.RowDefinitions>
                <RowDefinition/>
                <RowDefinition/>
            </Grid.RowDefinitions>
            <Grid x:Name="wordGrid" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
            <TextBlock Text="Akasztófa" FontSize="60" VerticalAlignment="Center" HorizontalAlignment="Center"/>
        </Grid>

        //Bal alsó grid
        <Grid x:Name="lettersGrid" Grid.Column="0" Grid.Row="1">
            <Grid.RowDefinitions>
                <RowDefinition></RowDefinition>
                <RowDefinition></RowDefinition>
                <RowDefinition></RowDefinition>
                <RowDefinition></RowDefinition>
                <RowDefinition></RowDefinition>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition></ColumnDefinition>
                <ColumnDefinition></ColumnDefinition>
                <ColumnDefinition></ColumnDefinition>
                <ColumnDefinition></ColumnDefinition>
                <ColumnDefinition></ColumnDefinition>
                <ColumnDefinition></ColumnDefinition>
                <ColumnDefinition></ColumnDefinition>
            </Grid.ColumnDefinitions>
            <Button x:Name="ButtonA" Content="A" FontSize="30" Grid.Row="0" Grid.Column="0" Click="Button_Click"/>
            <Button x:Name="ButtonÁ" Content="Á" FontSize="30" Grid.Row="0" Grid.Column="1" Click="Button_Click"/>
            <Button x:Name="ButtonB" Content="B" FontSize="30" Grid.Row="0" Grid.Column="2" Click="Button_Click"/>
            <Button x:Name="ButtonC" Content="C" FontSize="30" Grid.Row="0" Grid.Column="3" Click="Button_Click"/>
            <Button x:Name="ButtonD" Content="D" FontSize="30" Grid.Row="0" Grid.Column="4" Click="Button_Click"/>
            <Button x:Name="ButtonE" Content="E" FontSize="30" Grid.Row="0" Grid.Column="5" Click="Button_Click"/>
            ...
            <Button x:Name="ButtonY" Content="Y" FontSize="30" Grid.Row="4" Grid.Column="5" Click="Button_Click"/>
            <Button x:Name="ButtonZ" Content="Z" FontSize="30" Grid.Row="4" Grid.Column="6" Click="Button_Click"/>
        </Grid>

        //Jobb felső grid
        <Grid x:Name="attemptsGrid" Grid.Row="0" Grid.Column="1">
            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition></RowDefinition>
                    <RowDefinition></RowDefinition>
                </Grid.RowDefinitions>
                <Button x:Name="ButtonNext" Click="ButtonNext_Click" Content="Következő" FontSize="30" Grid.Row="1" Margin="160,40,160,40"/>
            </Grid>
            <TextBlock x:Name="attemptsCount" FontSize="50" TextWrapping="Wrap" Margin="30,0,0,0" HorizontalAlignment="Left" VerticalAlignment="Top"/>
        </Grid>

        //Bal alsó grid
        <Grid x:Name="hangmanGrid" Grid.Row="1" Grid.Column="1">
            <Image x:Name="hangmanImage"/>
        </Grid>
    </Grid>
</Window>
```

6.  - Felveszünk egy words listát, ez fogja tárolni az összes szót a txt fájlunkból
    - A string currentWord, ide fog lementődni egy randomszó
    - Button alapú Lista - wordButtons, ez fogja tárolni a random generált szónak a betűjeit buttonokban
    - Az int mistakeCount fogja

```C#
public List<string> words = new List<string>();
public string currentWord;
private List<Button> wordButtons = new List<Button>();
public int mistakeCount = 0;
```

7. A Button_Click metódus-ban meg kell állapítani, hogy melyik gombon történt a kattintás, ezt az object típusú sender paraméter tartalmazza. Az object típus lehetővé teszi, hogy bármilyen típusú objektumot átadjunk az eseménykezelőnek, mert minden típus az object típusból származik, így az object típusú referencia bármilyen típusú objektumra mutathat. Az eseménykezelőn belül típuskonverziót végzünk a sender paraméteren, hogy a konkrét típusú objektumhoz férjünk hozzá. Például, ha a sender egy gomb, akkor Button típusra konvertáljuk, hogy a gomb tulajdonságait módosíthassuk. 
is operátor: ellenőrízzük, hogy a sender egy adott típusú objektum-e.

```C#
private void Button_Click(object sender, RoutedEventArgs e)
{
    if (sender is Button button && button.Content == null)
    {
        
    }
}
```

8. Ha sikerül Button típusként azonosítani a sender-t, akkor kiolvassuk az adott button helyzetét és ezt felvesszük sor és oszlopként int változókba.

```C#
private void Button_Click(object sender, RoutedEventArgs e)
{
    if (sender is Button button && button.Content == null)
    {
        int row = Grid.GetRow(button);
        int col = Grid.GetColumn(button);
    }
}
```

9. Majd megnézzük a player1 változónkant amit kivűl létre hoztunk, hogy igaz e? Ha igaz, akkor az adott button-nak a Contentjét, tehát a szövegét beállítjuk X-re, a player1 érték false lesz (ezért legközelebb O-t rak le) és átadja a button kordinátáját az amőba mátrixnak, és a helyére egy 1-est írunk be, ez fogja az X-eket jelölni a mátrixban. Viszont legközelebb ha rámegyünk a gombra, akkor a player1 változónk már hamis, ezért a button szövege O lesz, és vissza állítjuk igazra a player1-et így mindig egyszer X-et egyszer, O-t rakunk le. És persze ennek is átadjuk a kordinátáját, és az O gombokat 2-vel jelöljük. És itt létrekell hozzunk egy WinnerCheck() függvényt, ahol minden kattintáskor ellenőrizzük hogy valaki nem e nyert e

```C#
private void Button_Click(object sender, RoutedEventArgs e)
{
    if (sender is Button button && button.Content == null)
    {
        int row = Grid.GetRow(button);
        int col = Grid.GetColumn(button);
        if (player1)
        {
            button.Content = "x";
            player1 = false;
            amoba[row, col] = 1;
        }
        else
        {
            button.Content= "o";
            player1 = true;
            amoba[row,col] = 2;
        }
        WinnerCheck();
    }
}
```

10. A WinnerCheck() függvényben ahogy említettem minden gombnyomásnál megnézzük hogy nyert e vagy az X vagy az O. Először felveszünk egy üres string változót winner névvel, ide fogjuk elmenteni a nyertest (x vagy o). 

```C#
private void WinnerCheck()
{
    string winner = string.Empty;
}
```

11. 3 nagy esetben nyerhet valamelyik fél. Vagy oszloposan 3 féle képen, vagy vízszintesen 3 féle képen, vagy átlósan 2 féle képen. A C# úgy kezeli a két dimenziós tömböket, hogyha végig megyünk rajtuk egy for-al, akkor a "amoba.GetLength(0)" a "0" mindig a sorok számát veszi át (a mi esetünkben ez 3 most) és az "amoba.GetLength(1)" az "1" mindig az oszlopok számát veszi át (ez is 3 a mi esetünkben, de ha a mátrixnak különböző az értéke, akkor ez változik) 
    1. *Oszloposan megyünk végig rajta:*
    For ciklussal végig megyünk a mátrixon, 0-ról kezdünk és addig megyünk, ahány sorból áll a mátrix, ezt tudjuk a amoba.GetLength(0)-val elérni. A for cikloson belül, megnézzük hogy ha egy adott oszlop, összes értéke 1, akkor X nyer, ha nem 1 akkor O nyer. Mivel oszlopokat nézünk, ezért a mátrix első értéke mindig ugyan azok lesznek (0, 1, 2), mert az jelöli a sorokat, és csak a második értéke lesz az *i*-edik elem, így tudjuk megnézni az összes oszlopot (Az első oszlop adatai a mi esetünkben **0,0; 1,0 és 2,0**). És ezeket meg nézzük hogy az értékük egyenlőek-e. És ha van egy nyertes, akkor egy break a for végén, hogy ne ellenőrizze tovább.
    ```C#
    for (int i = 0; i < amoba.GetLength(0); i++)
    {
        if (amoba[0,i]!= 0 && amoba[0,i] == amoba[1,i] && amoba[1,i] == amoba[2,i])
        {
            winner = amoba[0, i] == 1 ? "x" : "o";
            break;
        }
    }
    ```
    2. *Vízszintesen megyünk végig rajta:*
    Ez ugyan úgy működik mint amikor az oszlopokat ellenőriztük, csak a amoba.GetLength(1) itt 1 lesz az érték mivel itt most egy sor a mi esetünkben 3 oszlop tartalma. És az amőba mátrix első értéke lesz így az *i*-edik érték, mert az fog változni mint oszlopok, és a második érték az vagy 0 vagy 1 vagy 2. És ezeket meg nézzük hogy az értékük egyenlőek-e. És ha van egy nyertes, akkor egy break a for végén, hogy ne ellenőrizze tovább.
    ```C#
    for (int i = 0;i < amoba.GetLength(1); i++)
    {
        if (amoba[i, 0] != 0 && amoba[i, 0] == amoba[i, 1] && amoba[i, 1] == amoba[i, 2])
        {
            winner = amoba[i, 0] == 1 ? "x" : "o";
            break;
        }
    }
    ```
    3. *Átlósan ellenőrizzük le:*
    Itt két féle képen lehet alaki nyertes, ilyen X alakból az egyik átló. A középső, mátrixban 1,1-elem azon mindenféle képpen át fog menni, és megnézzük hogy megvan e az egyik átló \ vagy a másik átló /. És itt mivel az 1,1 az mind két esetben benne van, azt leellenőrizzük, hogyha 1 az értéke akkor X, ha nem 1 akkor O nyert.
    ```C#
    if ((amoba[0,0] != 0 && amoba[0,0] == amoba[1, 1] && amoba[1, 1] == amoba[2, 2]) ||
            (amoba[0, 2] != 0 && amoba[0, 2] == amoba[1, 1] && amoba[1, 1] == amoba[2, 0]))
    {
        winner = amoba[1, 1] == 1 ? "x" : "o";
    }
    ```

12. A WinnerCheck() végén, meg kell nézni hogy a winner, ha már nem üres, akkor kiírja a nyertest egy felugró üzenetbe, és lefuttatjuk a Restart() függvényt, hogy újra induljon a játék.
```C#
private void WinnerCheck()
{
    string winner = string.Empty;
    //columncheck
    for (int i = 0; i < amoba.GetLength(0); i++)
    {
        if (amoba[0,i]!= 0 && amoba[0,i] == amoba[1,i] && amoba[1,i] == amoba[2,i])
        {
            winner = amoba[0, i] == 1 ? "x" : "o";
            break;
        }
    }
    //rowcheck
    for (int i = 0;i < amoba.GetLength(1); i++)
    {
        if (amoba[i, 0] != 0 && amoba[i, 0] == amoba[i, 1] && amoba[i, 1] == amoba[i, 2])
        {
            winner = amoba[i, 0] == 1 ? "x" : "o";
            break;
        }
        }
        //crosscheck
        if ((amoba[0,0] != 0 && amoba[0,0] == amoba[1, 1] && amoba[1, 1] == amoba[2, 2]) ||
            (amoba[0, 2] != 0 && amoba[0, 2] == amoba[1, 1] && amoba[1, 1] == amoba[2, 0]))
        {
            winner = amoba[1, 1] == 1 ? "x" : "o";
        }
        if (winner != "")
        {
            MessageBox.Show(winner);
            Restart();
        }
}
```

13. Restart() függvényben az összes button szövegét null-ra állítjuk, így a kijelzőn törlödik az összes X és O. Majd egy dupla for ciklussal végig megyünk a mátrixon, és minden elemét vissza álltijuk 0-ra. És ekkor ha a felugró üzenetet le OK-oljál, kezdődhet újra a játék.

```C#
private void Restart()
{
    Button00.Content = null;
    Button01.Content = null;
    Button02.Content = null;
    Button10.Content = null;
    Button11.Content = null;
    Button12.Content = null;
    Button00.Content = null;
    Button21.Content = null;
    Button22.Content = null;
    for (int i = 0; i < 3; i++)
    {
        for (int j = 0; j < 3; j++)
        {
            amoba[i, j] = 0;
        }
    }
}
```
<details>
<summary>Nyiss le az xaml forrásért!</summary> 

```xaml
<Window x:Class="HangMan.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:HangMan"
        mc:Ignorable="d"
        Title="Hangman" Height="600" Width="1200">
    <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition></ColumnDefinition>
            <ColumnDefinition></ColumnDefinition>
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition></RowDefinition>
            <RowDefinition></RowDefinition>
        </Grid.RowDefinitions>
        <Grid x:Name="mainGrid" Grid.Row="0" Grid.Column="0">
            <Grid.RowDefinitions>
                <RowDefinition/>
                <RowDefinition/>
            </Grid.RowDefinitions>
            <Grid x:Name="wordGrid" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
            <TextBlock Text="Akasztófa" FontSize="60" VerticalAlignment="Center" HorizontalAlignment="Center"/>
        </Grid>
        <Grid x:Name="lettersGrid" Grid.Column="0" Grid.Row="1">
            <Grid.RowDefinitions>
                <RowDefinition></RowDefinition>
                <RowDefinition></RowDefinition>
                <RowDefinition></RowDefinition>
                <RowDefinition></RowDefinition>
                <RowDefinition></RowDefinition>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition></ColumnDefinition>
                <ColumnDefinition></ColumnDefinition>
                <ColumnDefinition></ColumnDefinition>
                <ColumnDefinition></ColumnDefinition>
                <ColumnDefinition></ColumnDefinition>
                <ColumnDefinition></ColumnDefinition>
                <ColumnDefinition></ColumnDefinition>
            </Grid.ColumnDefinitions>
            <Button x:Name="ButtonA" Content="A" FontSize="30" Grid.Row="0" Grid.Column="0" Click="Button_Click"/>
            <Button x:Name="ButtonÁ" Content="Á" FontSize="30" Grid.Row="0" Grid.Column="1" Click="Button_Click"/>
            <Button x:Name="ButtonB" Content="B" FontSize="30" Grid.Row="0" Grid.Column="2" Click="Button_Click"/>
            <Button x:Name="ButtonC" Content="C" FontSize="30" Grid.Row="0" Grid.Column="3" Click="Button_Click"/>
            <Button x:Name="ButtonD" Content="D" FontSize="30" Grid.Row="0" Grid.Column="4" Click="Button_Click"/>
            <Button x:Name="ButtonE" Content="E" FontSize="30" Grid.Row="0" Grid.Column="5" Click="Button_Click"/>
            <Button x:Name="ButtonÉ" Content="É" FontSize="30" Grid.Row="0" Grid.Column="6" Click="Button_Click"/>
            <Button x:Name="ButtonF" Content="F" FontSize="27" Grid.Row="1" Grid.Column="0" Click="Button_Click"/>
            <Button x:Name="ButtonG" Content="G" FontSize="30" Grid.Row="1" Grid.Column="1" Click="Button_Click"/>
            <Button x:Name="ButtonH" Content="H" FontSize="30" Grid.Row="1" Grid.Column="2" Click="Button_Click"/>
            <Button x:Name="ButtonI" Content="I" FontSize="30" Grid.Row="1" Grid.Column="3" Click="Button_Click"/>
            <Button x:Name="ButtonÍ" Content="Í" FontSize="30" Grid.Row="1" Grid.Column="4" Click="Button_Click"/>
            <Button x:Name="ButtonJ" Content="J" FontSize="30" Grid.Row="1" Grid.Column="5" Click="Button_Click"/>
            <Button x:Name="ButtonK" Content="K" FontSize="30" Grid.Row="1" Grid.Column="6" Click="Button_Click"/>
            <Button x:Name="ButtonL" Content="L" FontSize="30" Grid.Row="2" Grid.Column="0" Click="Button_Click"/>
            <Button x:Name="ButtonM" Content="M" FontSize="30" Grid.Row="2" Grid.Column="1" Click="Button_Click"/>
            <Button x:Name="ButtonN" Content="N" FontSize="30" Grid.Row="2" Grid.Column="2" Click="Button_Click"/>
            <Button x:Name="ButtonO" Content="O" FontSize="30" Grid.Row="2" Grid.Column="3" Click="Button_Click"/>
            <Button x:Name="ButtonÓ" Content="Ó" FontSize="30" Grid.Row="2" Grid.Column="4" Click="Button_Click"/>
            <Button x:Name="ButtonÖ" Content="Ö" FontSize="30" Grid.Row="2" Grid.Column="5" Click="Button_Click"/>
            <Button x:Name="ButtonŐ" Content="Ő" FontSize="30" Grid.Row="2" Grid.Column="6" Click="Button_Click"/>
            <Button x:Name="ButtonP" Content="P" FontSize="30" Grid.Row="3" Grid.Column="0" Click="Button_Click"/>
            <Button x:Name="ButtonQ" Content="Q" FontSize="30" Grid.Row="3" Grid.Column="1" Click="Button_Click"/>
            <Button x:Name="ButtonR" Content="R" FontSize="30" Grid.Row="3" Grid.Column="2" Click="Button_Click"/>
            <Button x:Name="ButtonS" Content="S" FontSize="30" Grid.Row="3" Grid.Column="3" Click="Button_Click"/>
            <Button x:Name="ButtonT" Content="T" FontSize="30" Grid.Row="3" Grid.Column="4" Click="Button_Click"/>
            <Button x:Name="ButtonU" Content="U" FontSize="30" Grid.Row="3" Grid.Column="5" Click="Button_Click"/>
            <Button x:Name="ButtonÚ" Content="Ú" FontSize="30" Grid.Row="3" Grid.Column="6" Click="Button_Click"/>
            <Button x:Name="ButtonÜ" Content="Ü" FontSize="30" Grid.Row="4" Grid.Column="0" Click="Button_Click"/>
            <Button x:Name="ButtonŰ" Content="Ű" FontSize="30" Grid.Row="4" Grid.Column="1" Click="Button_Click"/>
            <Button x:Name="ButtonV" Content="V" FontSize="30" Grid.Row="4" Grid.Column="2" Click="Button_Click"/>
            <Button x:Name="ButtonW" Content="W" FontSize="30" Grid.Row="4" Grid.Column="3" Click="Button_Click"/>
            <Button x:Name="ButtonX" Content="X" FontSize="30" Grid.Row="4" Grid.Column="4" Click="Button_Click"/>
            <Button x:Name="ButtonY" Content="Y" FontSize="30" Grid.Row="4" Grid.Column="5" Click="Button_Click"/>
            <Button x:Name="ButtonZ" Content="Z" FontSize="30" Grid.Row="4" Grid.Column="6" Click="Button_Click"/>
        </Grid>
        <Grid x:Name="attemptsGrid" Grid.Row="0" Grid.Column="1">
            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition></RowDefinition>
                    <RowDefinition></RowDefinition>
                </Grid.RowDefinitions>
                <Button x:Name="ButtonNext" Click="ButtonNext_Click" Content="Következő" FontSize="30" Grid.Row="1" Margin="160,40,160,40"/>
            </Grid>
            <TextBlock x:Name="attemptsCount" FontSize="50" TextWrapping="Wrap" Margin="30,0,0,0" HorizontalAlignment="Left" VerticalAlignment="Top"/>
        </Grid>
        <Grid x:Name="hangmanGrid" Grid.Row="1" Grid.Column="1">
            <Image x:Name="hangmanImage"/>
        </Grid>
    </Grid>
</Window>

```
</details>

<details>
<summary>Nyiss le az xaml.cs forrásért!</summary>

```C#
using System.Text;
using System.Windows;
using System.Windows.Controls;
using System.Windows.Media;
using System.Windows.Media.Imaging;
using System.IO;
using System.Linq;
using System.Collections.Generic;

namespace HangMan
{
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
            LoadWords();
            InitializeWord();
        }

        public List<string> words = new List<string>();
        public string currentWord;
        private List<Button> wordButtons = new List<Button>();
        public int mistakeCount = 0;

        private void LoadWords()
        {
            using (StreamReader sr = new StreamReader("szavak.txt", Encoding.UTF8))
            {
                string line;
                while ((line = sr.ReadLine()) != null)
                {
                    words.Add(line);
                }
            }
        }

        public string GetRandomWord()
        {
            Random rnd = new Random();
            int randomIndex = rnd.Next(words.Count);
            return words[randomIndex];
        }

        private void InitializeWord()
        {
            List<Button> buttons = new List<Button>();
            currentWord = GetRandomWord();

            for (int i = 0; i < currentWord.Length; i++)
            {
                wordGrid.ColumnDefinitions.Add(new ColumnDefinition());

                Button btn = new Button();
                btn.Content = "_";
                btn.Tag = currentWord[i].ToString();
                btn.Name = "LetterButton" + i;
                btn.FontSize = 30;
                btn.Width = 50;
                btn.Height = 50;
                btn.Margin = new Thickness(5);

                wordGrid.Children.Add(btn);
                Grid.SetColumn(btn, i);
                wordButtons.Add(btn);
            }
        }

        private void ResetGame()
        {
            mistakeCount = 0;
            attemptsCount.Text = "";
            currentWord = GetRandomWord();
            wordGrid.Children.Clear();
            wordGrid.ColumnDefinitions.Clear();
            wordButtons.Clear();

            for (int i = 0; i < currentWord.Length; i++)
            {
                wordGrid.ColumnDefinitions.Add(new ColumnDefinition());

                Button btn = new Button();
                btn.Content = "_";
                btn.Tag = currentWord[i].ToString();
                btn.Name = "LetterButton" + i;
                btn.FontSize = 30;
                btn.Width = 50;
                btn.Height = 50;
                btn.Margin = new Thickness(5);

                wordGrid.Children.Add(btn);
                Grid.SetColumn(btn, i);
                wordButtons.Add(btn);
            }

            foreach (var child in lettersGrid.Children)
            {
                if (child is Button button)
                {
                    button.IsEnabled = true;
                    button.Background = Brushes.LightGray;
                }
            }

            hangmanImage.Source = null;
        }

        private void CheckLoss()
        {
            if (mistakeCount == 11)
            {
                foreach (var child in lettersGrid.Children)
                {
                    if (child is Button button)
                    {
                        button.IsEnabled = false;
                    }
                }
                attemptsCount.Text = $"Sajnos vesztettél, a szó {currentWord} volt.";
            }
        }

        private void CheckWin()
        {
            if (wordButtons.All(b => b.Content.ToString() != "_"))
            {
                foreach (var child in lettersGrid.Children)
                {
                    if (child is Button button)
                    {
                        button.IsEnabled = false;
                    }
                }
                attemptsCount.Text = $"Gratulálok. {mistakeCount} rossz tippel kitaláltad!";
            }
        }

        private void Button_Click(object sender, RoutedEventArgs e)
        {
            if (sender is Button button)
            {
                if (mistakeCount <= 11)
                {
                    string letter = button.Content.ToString().ToLower();
                    if (currentWord.Contains(letter))
                    {
                        button.Background = Brushes.Green;
                        for (int i = 0; i < currentWord.Length; i++)
                        {
                            if (currentWord[i].ToString().ToLower() == letter)
                            {
                                wordButtons[i].Content = letter.ToUpper();
                            }
                        }
                        CheckWin();
                    }
                    else
                    {
                        button.IsEnabled = false;
                        hangmanImage.Source = new BitmapImage(new Uri($"C:\\Users\\1tc-selode\\OneDrive\\wpf\\HangMan\\HangMan\\bin\\Debug\\net8.0-windows\\Images\\akasztofa{mistakeCount}.png", UriKind.Absolute));
                        mistakeCount++;
                        CheckLoss();
                    }
                }
            }
        }

        private void ButtonNext_Click(object sender, RoutedEventArgs e)
        {
            ResetGame();
        }
    }
}
```
</details>