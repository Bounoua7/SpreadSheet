                                                                                       | 18/12/2021 |

<h1 style="color:#0B615E;  text-align:center; vertical-align: middle; padding:40px 0; margin-top:30px " > Application Using Main Window</h1>
             </br>
<h2 style="color:#0B615E;">
Create a MainWindow based application using the designer.  
<li>Text Editor.</li>
<li>SpreadSheet.</li>

</h2>
<h4 style="color:#088A85;">
  
Preface
  
</h4>
<p>

><strong>A Text editor </strong> example is a simple example of creating an application with Qt. Specifically, the example uses QML to set up the user interface, C++ to implement the file operations, and the Qt tools to manage the project files and build environment.
We will use Qt Quick Designer to add QML controls onto our application and set up the controls and resources. We will also implement the logic and connect the user interface to a C++ backend that will handle the saving and loading of text files.
<strong> A SpreadSheet</strong> or worksheet is a file made of rows and columns that help sort, organize, and arrange data efficiently, and calculate numerical data. What makes a spreadsheet software program unique is its ability to calculate values using mathematical formulas and the data in cells. An example of how a spreadsheet may be utilized is creating an overview of your bank's balance. For example; Today, Microsoft Excel is the most popular and widely used spreadsheet program, but there are also many alternatives

><strong>The goal</strong> of this report is to create a MainWindow based application that is Text editor  using Qt designer and a spreadsheet In the our last iteration of the SpreadSheet we did obtain an application with: Menu Bar,Two tools bars,Status bar to print the informations..  </p>

<ul>
<h3 style="color:#58ACFA";>

 <h4> <a href="#textedit-id">Text Editor</a></h4>

   <li>Creating the project.</li> 
   <li>Icons.</li>
   <li>Menus.</li> 
   <li>Actions.</li>
   <li>Functionnality.</li>

 <h4> <a href="#spreadsheet-id">SpreadSheet</a> </h4> 
   <li>Remarks</li>
   <li>Go Cell</li> 
   <li>Find Dialog</li>
   <li>Saving and loading files (Saving Content ,Save File action,Load File) </li>
</h3>
</ul>
      
------------
   <h3 style="color:#088A85" id="textedit-id" >Text Editor  </h3>
  We will playing the Designer for a fast application creation. The application is from Qt Examples and is a simple text editor program built around QPlainText.
We will mainly use the designer for a rapid design of it features. </br>
First thing we <strong> create a new project called WordText </strong> and we Choose a QT Widgets Application and Inherit from QMainWindow. </br>
We Open the form we'll find  an empty menu and already a defined menu and satatus bars.
Then we play with Qt designer and make our text editor. </br>
We add <strong>menus</strong> in the image description and <strong>actions</strong> of each menu by simply clicking the text in the menu.
Also we Add a resource file which contains a set of predefined <strong>icons</strong>  we have chosen.
Then we go to slot of each action, we choose triggered() in QAction signal and we try to code the the <strong>functionality </strong> in the mainwindow.cpp file.

<h5 style="color:#FF8000"> The main class code</h5>

```c++
#include "mainwindow.h"

#include <QApplication>

int main(int argc, char *argv[])
{
    QApplication a(argc, argv);
    MainWindow w;
    w.show();
    return a.exec();
}


```
<h5 style="color:#FF8000">mainwindow.h file:</h5>


```c++
#ifndef MAINWINDOW_H
#define MAINWINDOW_H

#include <QMainWindow>
#include<QFile>
#include<QString>
#include<QFileDialog>
#include<QTextStream>
#include<QMessageBox>
#include<QPrinter>
#include<QPrintDialog>
#include<QSyntaxHighlighter>
#include<QtQML>

QT_BEGIN_NAMESPACE
namespace Ui { class MainWindow; }
QT_END_NAMESPACE

class MainWindow : public QMainWindow
{
    Q_OBJECT

public:
    MainWindow(QWidget *parent = nullptr);
    ~MainWindow();

private slots:
    void on_actionAbout_triggered();

    void on_actionOpen_triggered();

    void on_actionNew_triggered();

    void on_actionSave_As_triggered();

    void on_actionPrint_triggered();

    void on_actionExit_triggered();

    void on_actionCopy_triggered();

    void on_actionPaste_triggered();

    void on_actionCut_triggered();

    void on_actionUndo_triggered();

    void on_actionRedo_triggered();

    void on_actionAbout_Qt_triggered();

   // void on_actionHighlighter_triggered();

    void on_actionSave_triggered();

    void on_actionBold_triggered();

    void on_textEdit_copyAvailable(bool b);

    void on_actionRight_triggered();



protected:
    //void highlightBlock(const QString &text) ;

private:
    bool bold;
    bool italic;
    bool underline;
    QString  currentFile= "";
    QString  fileName;


private:
    Ui::MainWindow *ui;
};
#endif // MAINWINDOW_H



```
<h5 style="color:#FF8000">mainwindow.cpp file:</h5>

```c++

 #include "mainwindow.h"
#include "ui_mainwindow.h"
#include<QSyntaxHighlighter>
#include <QtQml>
#include <QFont>

MainWindow::MainWindow(QWidget *parent)
    : QMainWindow(parent)
    , ui(new Ui::MainWindow)
{
    ui->setupUi(this);
    this->setCentralWidget(ui->textEdit);

}

MainWindow::~MainWindow()
{
    delete ui;
}




void MainWindow::on_actionOpen_triggered()
{
    QString filename = QFileDialog::getOpenFileName(this,"Open the file");
    QFile file(fileName);
    currentFile = fileName;
    ui->actionOpen->setShortcut(tr("Ctrl+O"));
    if(!file.open(QIODevice::ReadOnly | QFile::Text))
    {
        QMessageBox::warning(this,"Warning", "Cannot open file :" + file.errorString());
        return;
    }
    setWindowTitle(fileName);
    QTextStream in(&file);
    QString text = in.readAll();
    ui->textEdit->setPlainText(text);
    file.close();
}


void MainWindow::on_actionNew_triggered()
{
    currentFile.clear();
    ui->textEdit->setPlainText(QString());
    ui->actionNew->setShortcut(tr("Ctrl+N"));
}

void MainWindow::on_actionSave_triggered()
{
    QString fileName = QFileDialog::getSaveFileName(this);
       QFile file(fileName);
       if(file.open(QIODevice::WriteOnly))
       {
           currentFile = fileName;
           setWindowTitle(fileName);

           QTextStream out(&file);
           QString text = ui->textEdit->toPlainText();
           out << text;
       }
       file.close();
}

void MainWindow::on_actionSave_As_triggered()
{
    QString fileName = QFileDialog::getSaveFileName(this,"Save as");
    QFile file(fileName);
    ui->actionSave_As->setShortcut(tr("Ctrl+Shift+S"));
    if(!file.open(QFile::WriteOnly | QFile::Text))
    {
        QMessageBox::warning(this,"Warning", "Cannot save file :" + file.errorString());
        return;
    }
    currentFile= fileName;
    setWindowTitle(fileName);
    QTextStream out(&file);
    QString text = ui->textEdit->toPlainText();
    out << text;
    file.close();

}


void MainWindow::on_actionPrint_triggered()
{
    QPrinter printer;
    printer.setPrinterName("Printer Name");
    QPrintDialog pDialog(&printer,this);
    if(pDialog.exec() == QDialog::Rejected)
    {
        QMessageBox::warning(this,"warning", "Cannot Access printer");
        return;
    }
    ui->textEdit->print(&printer);
    ui->actionPrint->setShortcut(tr("Ctrl+P"));
}


void MainWindow::on_actionExit_triggered()
{
    QApplication::quit();
    ui->actionExit->setShortcut(tr("Ctrl+Q"));
}


void MainWindow::on_actionCopy_triggered()
{
    ui->textEdit->copy();
      ui->actionCopy->setShortcut(tr("Ctrl+C"));
}


void MainWindow::on_actionPaste_triggered()
{
    ui->textEdit->paste();
     ui->actionPaste->setShortcut(tr("Ctrl+V"));
}


void MainWindow::on_actionCut_triggered()
{
       ui->textEdit->cut();
          ui->actionCut->setShortcut(tr("Ctrl+X"));
}


void MainWindow::on_actionUndo_triggered()
{
        ui->textEdit->undo();
         ui->actionUndo->setShortcut(tr("Ctrl+Z"));
}


void MainWindow::on_actionRedo_triggered()
{
        ui->textEdit->redo();
         ui->actionRedo->setShortcut(tr("Ctrl+Y"));
}

void MainWindow::on_actionAbout_triggered()
{
    QMessageBox::information(this,"About","Word Editor Application");
}

void MainWindow::on_actionAbout_Qt_triggered()
{
    QApplication::aboutQt();

}


/*void MainWindow::on_actionHighlighter_triggered()
{      for (const HighlightingRule &rule : qAsConst(highlightingRules)) {
        QRegularExpressionMatchIterator matchIterator = rule.pattern.globalMatch(text);
        while (matchIterator.hasNext()) {
            QRegularExpressionMatch match = matchIterator.next();
            setFormat(match.capturedStart(), match.capturedLength(), rule.format);
    ui->textEdit->highlighter();
}
*/

 ```
<h5 style="color:#FF8000">Text Editor  result:</h5>






https://user-images.githubusercontent.com/86807424/146686487-7d1f30aa-9231-4c05-a184-d8cda4d72a4c.mp4




   <h3 style="color:#088A85;" id="spreadsheet-id" > SpreadSheet </h3>
   
   we write the code for the graphical and set of actions for our main SpreadSeet application. ans we write a set of basic functionality.


<h5 style="color:#FF8000"> main file:</h5>

```
#include "spreadsheet.h"

#include <QApplication>

int main(int argc, char *argv[])
{
    QApplication a(argc, argv);
    SpreadSheet w;
    w.show();
    return a.exec();
}


```

<h5 style="color:#FF8000"> spreadsheet.h file:</h5>

```c++
#ifndef SPREADSHEET_H
#define SPREADSHEET_H

#include <QMainWindow>
#include <QTableWidget>
#include <QAction>
#include <QMenu>
#include <QToolBar>
#include <QLabel>
#include <QStatusBar>
#include <QPixmap>
#include <QMenuBar>
#include <QToolBar>
#include <QFileDialog>
#include <QTextStream>
#include <QDebug>
#include <QMimeData>
#include <QClipboard>
#include <QLineEdit>
#include <QApplication>
#include <QMessageBox>
#include "godialog.h"
#include "finddialog.h"

class SpreadSheet : public QMainWindow
{
    Q_OBJECT

public:
    SpreadSheet(QWidget *parent = nullptr);
    ~SpreadSheet();

protected:
    void setupMainWidget();
    void createActions();
    void createMenus();
    void createToolBars();
    void makeConnexions();
    void saveContent(QString fileName)const;
    void saveAs(QString fileName)const;
    void loadContent(QString fileName);
    void loadCSVFile(QString fileName);
private slots:
    void close();
    void updateStatusBar(int, int); //Respond for the call changed
    void aboutQtSlot();
    void goCellSlot();
    void findTextSlot();
    void saveSlot();
    void loadSlot();
    void loadCSVSlot();
    void saveAsSlot();
    void openRecentFile();
    void newFileSlot();
    void cutSlot();
    void copySlot();
    void pasteSlot();


 //Pointers
private:
    //  Central Widget
    QTableWidget *spreadsheet;


    //Menus
    QMenu *fileMenu;
    QMenu *editMenu;
    QMenu *toolsMenu;
    QMenu *optionsMenu;
    QMenu *helpMenu;

    //  Menu file Actions
    QAction * newFile;
    QAction * open;
    QAction * save;
    QAction * save_As;
    QAction * quit;
    QAction * exit;
    QAction * Delete;

    QAction * openCSVFile;

    // Edit actions
    QAction *cut;
    QAction *copy;
    QAction *paste;
    QAction *deleteAction;
    QAction *find;
    QAction *row;
    QAction *Column;
    QAction *all;
    QAction *goCell;


    //toolsMenu actions
    QAction *recalculate;
    QAction *sort;


    //optionsMenu actions
    QAction *showGrid;
    QAction *auto_recalculate;
    QAction *about;
    QAction *aboutQt;

    //recent file action
     QVector <QAction*> recentFileActions;

    // Widget pour la bare d'etat
    QLabel *cellLocation;  //position de la cellule active
    QLabel *cellFormula;   // Formuel de la cellue active

       QString * currentFile;  //Répertoire du fichier

       QString baseName;  //Nom de base de fichier

       QString * previousFile;  //storing the previous file

       QClipboard *clipboard; //Clipboard

       int count = 0;   //Counter of maxRecentFile



};

#endif // SPREADSHEET_H



```

<h5 style="color:#FF8000">spreadsheet.cpp file:</h5> 


```c++
#include "spreadsheet.h"
#include <QPixmap>
#include <QMenuBar>
#include <QToolBar>
#include <QApplication>
#include <QMessageBox>


SpreadSheet::SpreadSheet(QWidget *parent)
    : QMainWindow(parent)
{
    //Seting the spreadsheet

    setupMainWidget();

    createActions();

    createMenus();

    createToolBars();

    makeConnexions();


    //Creating the labels for the status bar (should be in its proper function)
    cellLocation = new QLabel("(1, 1)");
    cellFormula = new QLabel("");
    statusBar()->addPermanentWidget(cellLocation);
    statusBar()->addPermanentWidget(cellFormula);
}

void SpreadSheet::setupMainWidget()
{
    spreadsheet = new QTableWidget;
    spreadsheet->setRowCount(30);
    spreadsheet->setColumnCount(30);
    setCentralWidget(spreadsheet);

}

SpreadSheet::~SpreadSheet()
{
    delete spreadsheet;

    // --------------- Actions       --------------//
    delete  newFile;
    delete  open;
    delete  save;
    delete  save_As;
    delete  exit;
    delete cut;
    delete copy;
    delete paste;
    delete deleteAction;
    delete find;
    delete row;
    delete Column;
    delete all;
    delete goCell;
    delete recalculate;
    delete sort;
    delete showGrid;
    delete auto_recalculate;
    delete about;
    delete aboutQt;
    delete openCSVFile;

    // ---------- Menus ----------
    delete FileMenu;
    delete editMenu;
    delete toolsMenu;
    delete optionsMenu;
    delete helpMenu;
}

void SpreadSheet::createActions()
{
    // new action
   QPixmap newIcon(":/new_file.png");
   newFile = new QAction(newIcon, "&New", this);
   newFile->setShortcut(tr("Ctrl+N"));


    // open action
   open = new QAction("&Open", this);
   open->setShortcut(tr("Ctrl+O"));

    // save action
   save = new QAction("&Save", this);
   save->setShortcut(tr("Ctrl+S"));

    // Save as action
   save_As = new QAction("save &As", this);


    // cut action
   QPixmap cutIcon(":/cut_icon.png");
   cut = new QAction(newIcon, "Cu&t", this);
   cut->setShortcut(tr("Ctrl+X"));

   // copy
   copy = new QAction( "&Copy", this);
   copy->setShortcut(tr("Ctrl+C"));

   // paste action
   paste = new QAction( "&Paste", this);
   paste->setShortcut(tr("Ctrl+V"));

  // delete action
   deleteAction = new QAction( "&Delete", this);
   deleteAction->setShortcut(tr("Del"));
  // quit action
   quit = new QAction("&Quit", this);
   //row action
   row  = new QAction("&Row", this);
   //column action
   Column = new QAction("&Column", this);
   // select all action
   all = new QAction("&All", this);
   all->setShortcut(tr("Ctrl+A"));

   //find text

    find = new QAction("&Find...",this);
    // go cell
       goCell = new QAction("&Go to Cell...",this);
   // recalculate
       recalculate = new QAction("&Recalculate", this);
   //sort
       sort = new QAction("&Sort...", this);
   //showGrid
       showGrid = new QAction("Show Grid",this);
       showGrid->setCheckable(true);
       showGrid->setChecked(spreadsheet->showGrid());
   //autorecalculate action
       auto_recalculate = new QAction("Auto-Recalculate", this);
       auto_recalculate->setCheckable(true);
   // about actions
       about = new QAction("About",this);
       aboutQt = new QAction("About &Qt",this);


       QPixmap new_file(":/icons/new_file.png");
         QPixmap openIcon(":/icons/open_file.png");
         QPixmap saveIcon(":/icons/save_file");
         QPixmap quitIcon(":/icons/quit_icon.png");

         QPixmap cut_icon(":/icons/cut_icon.png");
         QPixmap go_to_icon(":/icons/go_to_icon.png");
         QPixmap search_icon(":/icons/search_icon.png");
         QPixmap csvIcon(":/icons/openCSV.png");


   QPixmap findIcon(":/search_icon.png"); find= new QAction(newIcon, "&Find", this);
   find->setShortcut(tr("Ctrl+F"));

   QPixmap goCellIcon(":/go_to_icon.png");
   goCell = new QAction( goCellIcon, "&Go to Cell", this);
   deleteAction->setShortcut(tr("f5"));


   recalculate = new QAction("&Recalculate",this);
   recalculate->setShortcut(tr("F9"));


   sort = new QAction("&Sort");



   showGrid = new QAction("&Show Grid");
   showGrid->setCheckable(true);
   showGrid->setChecked(spreadsheet->showGrid());

   auto_recalculate = new QAction("&Auto-recalculate");
   auto_recalculate->setCheckable(true);
   auto_recalculate->setChecked(true);


   //Etape 2 : Creer l'action   
     newFile = new QAction(newIcon,"&New",this);
     open = new QAction(openIcon, "&Open Coordinate File...",this);
     openCSVFile = new QAction(csvIcon, "Open CSV File", this);
     save = new QAction(saveIcon, "&Save",this);
     save_As = new QAction(saveIcon, "Save &As...",this);
     quit = new QAction(quitIcon, "&Quit", this);
 
     cut = new QAction(cutIcon, "&Cut",this);
     copy = new QAction( "&Copy", this);
     paste = new QAction( "&Paste", this);
     Delete = new QAction("&Delete", this);
     row = new QAction("&Row",this);
     Column = new QAction("&Column",this);
     all = new QAction("&All",this);
     find = new QAction("&Find...",this);
     goCell = new QAction("&Go to Cell...",this);
 
     recalculate = new QAction("&Recalculate", this);
     sort = new QAction("&Sort...", this);
 
     showGrid = new QAction("Show Grid",this);
     showGrid->setCheckable(true);
     showGrid->setChecked(spreadsheet->showGrid());
     auto_recalculate = new QAction("Auto-Recalculate", this);
     auto_recalculate->setCheckable(true);
 
     about = new QAction("About",this);
     aboutQt = new QAction("About &Qt",this);
 
     //Etape 3 : Lui choisir un racourci
     newFile->setShortcut(tr("Ctrl+N"));
     open->setShortcut(tr("Ctrl+O"));
     save->setShortcut(tr("Ctrl+S"));
     save_As->setShortcut(tr("Ctrl+Shift+S"));
     quit->setShortcut(tr("Ctrl+Q"));
 
     cut->setShortcut(tr("Ctrl+X"));
     copy->setShortcut(tr("Ctrl+C"));
     paste->setShortcut(tr("Ctrl+V"));
     Delete->setShortcut(tr("Del"));
     all->setShortcut(tr("Ctrl+A"));
     find->setShortcut(tr("Ctrl+F"));
     goCell->setShortcut(tr("F5"));
     recalculate->setShortcut(tr("F9"));
 
     /* --- RecentFile actions --- */
     for (int i = 0; i < 5; ++i) {
            recentFileActions.push_back(new QAction(this));
            recentFileActions[i]->setVisible(false);
        }
 }
 
 void SpreadSheet::createMenus()
 {
     //Creer le menu file
     fileMenu = menuBar()->addMenu("&File");
     fileMenu->addAction(newFile);
     auto open = fileMenu->addMenu("Open");
     open->addAction(open);
     open->addAction(openCSVFile);
     fileMenu->addAction(save);
     fileMenu->addAction(save_As);
     fileMenu->addSeparator();
     for (int i = 0; i < 5; ++i)
             fileMenu->addAction(recentFileActions[i]);
     fileMenu->addSeparator();
     fileMenu->addAction(quit);
 
     //Creer le menu d'édition
     editMenu = menuBar()->addMenu("&Edit");
     editMenu->addAction(cut);
     editMenu->addAction(copy);
     editMenu->addAction(paste);
     editMenu->addAction(Delete);
     auto select = editMenu->addMenu("Select");
     select->addAction(row);
     select->addAction(Column);
     select->addAction(all);
     editMenu->addSeparator();
     editMenu->addAction(find);
     editMenu->addAction(goCell);
 
     //Ccréer le menu d'outils
     toolsMenu = menuBar()->addMenu("&Tools");
     toolsMenu->addAction(recalculate);
     toolsMenu->addAction(sort);
 
     //Creer le menu options
     optionsMenu = menuBar()->addMenu("&Options");
     optionsMenu->addAction(showGrid);
     optionsMenu->addAction(auto_recalculate);
 
     //Creer le menu help
     helpMenu = menuBar()->addMenu("&Help");
     helpMenu->addAction(about);
     helpMenu->addAction(aboutQt);;
 }
 
 void SpreadSheet::makeConnexions()
 {
     //Etape 4 : Lui choisir un slot
 
     connect(quit, &QAction::triggered, this, &QMainWindow::close);
 
     connect(aboutQt, &QAction::triggered, this, &SpreadSheet::aboutQtSlot);
 
     connect(all, &QAction::triggered, spreadsheet, &QTableWidget::selectAll);
 
     connect(row, &QAction::triggered, spreadsheet, &QTableWidget::selectRow);
 
     connect(cutFile, &QAction::triggered, this, &SpreadSheet::cutSlot);
 
     connect(copy, &QAction::triggered, this, &SpreadSheet::copySlot);
 
     connect(paste, &QAction::triggered, this, &SpreadSheet::pasteSlot);
 
     /* --- Connect newFile action --- */
     connect(newFile, &QAction::triggered, this, &SpreadSheet::newFileSlot);
 
     connect(Column, &QAction::triggered, spreadsheet, &QTableWidget::selectColumn);
 
     connect(showGrid, &QAction::triggered, spreadsheet, &QTableWidget::setShowGrid);
 
     //connectting the changee of any element in the spreadsheet with the update status bar
     connect(spreadsheet, &QTableWidget::cellClicked, this,  &SpreadSheet::updateStatusBar);
 
     //Connection pour le go to cell
     connect(goCell, &QAction::triggered,this, &SpreadSheet::goCellSlot);
 
     //Connection pour le find text
     connect(find, &QAction::triggered, this, &SpreadSheet::findTextSlot);
 
     //Connecter le save Action au save Slot
     connect(save, &QAction::triggered, this, &SpreadSheet::saveSlot);
 
     //Connecter le load Action au load Slot
     connect(open, &QAction::triggered, this, &SpreadSheet::loadSlot);
 
     //Connecter openCSVFile Action au loadCSVSlot
     connect(openCSVFile, &QAction::triggered, this, &SpreadSheet::loadCSVSlot);
 
     //Connecter le saveAs action au saveAsSlot
     connect(save_As &QAction::triggered, this, SpreadSheet::saveAsSlot);
 
     /* --- Connect RecentFile actions --- */
     for (int i = 0; i < 5; ++i) {
            connect(recentFileActions[i], &QAction::triggered,
                    this, &SpreadSheet::openRecentFile);
        }
 }
 
 void SpreadSheet::createToolBars()
 {
     auto toolBar1 = addToolBar("Tool Bar1");
     toolBar1->addAction(newFile);
     toolBar1->addAction(save);
     toolBar1->addAction(quit);
 
     auto toolBar2 = addToolBar("Tool Bar2");
     toolBar2->addAction(find);
     toolBar2->addAction(goCell);
 }
 
 void SpreadSheet::newFileSlot()
 {
     spreadsheet->clear();
     currentFile = nullptr;
     setWindowTitle("Untiteld");
 }
 
 void SpreadSheet::loadContent(QString fileName)
 {
     QFile file(fileName);
     if(file.open(QIODevice::ReadOnly))
     {
         QTextStream in(&file);
         QString line;
 
         while (!in.atEnd())
         {
             line = in.readLine();
             auto tokens = line.split(',');
             int row = tokens[0].toInt();
             int col = tokens[1].toInt();
             spreadsheet->setItem(row,col, new QTableWidgetItem(tokens[2]));
         }
     }
 
     file.close();
 }
 
 void SpreadSheet::loadSlot()
 {
     //1. Tester si on possède un nom de fichier
 
         //Creer factory design
         QFileDialog D; //factory
 
         //Creer un dialoque pour abtenir le nom du fichier
         auto fileName = D.getOpenFileName();
 
         if(fileName != "")
         {
             //Changer le nom du fichier courant
             currentFile = new QString(fileName);
 
             //2. charger le contrenu
             loadContent(*currentFile);
         }
 
         //Get the name of the file without the directory
         QFileInfo fi(*currentFile);
         QString baseName = fi.completeBaseName();
 
         //Mettre à jour le titre de la fenêtre
         setWindowTitle(baseName);
 
         //show the file in recentFileMenu
         if(count == 0)
         {
             recentFileActions[count]->setText(*currentFile);
             recentFileActions[count]->setVisible(true);
             previousFile = currentFile;
             count++;
         }
         else if(*previousFile != *currentFile && count != 0)
         {
             recentFileActions[count]->setText(*currentFile);
             recentFileActions[count]->setVisible(true);
             previousFile = currentFile;
             count++;
         }
          if(count > 5)
             count = 0;
 }
 
 void SpreadSheet::loadCSVFile(QString fileName){
    //Pointer sur le fichier d'intéret
     QFile file(fileName);
 
     //Tester si on a un fichier csv à lire
     if(!file.open(QFile::ReadOnly | QIODevice::Text))
         return;
     else
     {
        QTextStream in(&file);
        QStringList list;
        int count = 0;
        while (!in.atEnd())
        {
             QString line = in.readLine();
             for(auto item : line.split(","))
             {
                 list.append(item);
             }
             for(int i=0; i<list.size(); i++)
             {
                 spreadsheet->setItem(count, i, new QTableWidgetItem(list[i]));
             }
             count++;
             list.clear();
        }
     }
 
     file.close();
 }
 
 void SpreadSheet::loadCSVSlot()
 {
     //1. Tester si on possède un nom de fichier
 
             //Creer factory design
             QFileDialog D; //factory
 
             //Creer un dialoque pour abtenir le nom du fichier
             auto fileName = D.getOpenFileName();
 
             if(fileName != "")
             {
                 //Changer le nom du fichier courant
                 currentFile = new QString(fileName);
 
                 //2. charger le contrenu
                 loadCSVFile(*currentFile);
             }
 
             //Get the name of the file without the directory
             QFileInfo fi(*currentFile);
             QString baseName = fi.completeBaseName();
 
             //Mettre à jour le titre de la fenêtre
             setWindowTitle(baseName);
 
             //show the file in recentFileMenu
             if(count == 0)
             {
                 recentFileActions[count]->setText(*currentFile);
                 recentFileActions[count]->setVisible(true);
                previousFile = currentFile;
                 count++;
             }
             else if(*previousFile != *currentFile && count < 5)
             {
                 recentFileActions[count]->setText(*currentFile);
                 recentFileActions[count]->setVisible(true);
                 previousFile = currentFile;
                 count++;
             }
             if(count > 5)
                     count = 0;
 }
 
 void SpreadSheet::saveContent(QString fileName)const
 {
     //Syntaxe C++
     // ofstream out(fileName)  -------. /out.close();
 
     //Syntaxe C
     //FILE * fid = fopen(fileName, "w")         fclose(fid);
 
     //En Qt
 
     // un pointeur sur le fichier d'interet
     QFile file(fileName);
 
     //Ouvrir le fichier
     if(file.open(QIODevice::WriteOnly))
     {
         QTextStream out(&file);
 
         //Parcourir les cellules et sauvegarder leur contenu
         for ( int i = 0; i < spreadsheet->rowCount() ; i++ )
             for (int j = 0; j < spreadsheet->columnCount() ; j++ )
             {
                 auto cell = spreadsheet->item(i,j);
                 if (cell)
                 {
                     out << i << "," << j << "," <<cell->text() << Qt::endl;
                 }
             }
     }
 
     //Fermer le ficher
     file.close();
 }
 
 void SpreadSheet::saveSlot()
 {
     //1. Tester si on possède un nom de fichier
     if(!currentFile)
     {
         //Creer factory design
         QFileDialog D; //factory
 
         //Creer un dialoque pour abtenir le nom du fichier
         auto fileName = D.getSaveFileName();
 
         //Changer le nom du fichier courant
         currentFile = new QString(fileName);
 
         //2. Sauvegarder le contrenu
         saveContent(*currentFile);
     }
 
     //Get the name of the file without the directory
     QFileInfo fi(*currentFile);
     QString baseName = fi.completeBaseName();
 
     //Mettre à jour le titre de la fenêtre
     setWindowTitle(baseName);
 
     //show the file in recentFileMenu
     if(*currentFile != "")
     {
         if(count == 0)
         {
             recentFileActions[count]->setText(*currentFile);
             recentFileActions[count]->setVisible(true);
             previousFile = currentFile;
             count++;
         }
         else if(*previousFile != *currentFile && count != 0)
         {
             recentFileActions[count]->setText(*currentFile);
             recentFileActions[count]->setVisible(true);
            previousFile = currentFile;
             count++;
         }
         if(count > 5)
         {
             count = 0;
         }
     }
 }
 
 void SpreadSheet::saveAs(QString fileName)const{
     //Pointer sur le fichier
     QFile file(fileName);
 
     if(file.open(QIODevice::WriteOnly))
     {
         QTextStream out(&file);
         QString line = "";
         //Parcourir les cellules et sauvegarder leur contenu
         for ( int i = 0; i < spreadsheet->rowCount() ; i++ )
         {
             for (int j = 0; j < spreadsheet->columnCount() ; j++ )
             {
                 if(spreadsheet->item(i,j))
                 {
                     line += spreadsheet->item(i,j)->text();
                     line += ",";
                 }
                 else
                 {
                     line += " ";
                     line += ",";
                 }
             }
             out << line.remove(line.size()-1, 1) << Qt::endl;
             line = "";
         }
     }
 
     //Fermer le ficher
     file.close();
 }
 
 void SpreadSheet::saveAsSlot(){
     //1. Tester si on possède un nom de fichier
     if(!currentFile)
     {
         //Creer factory design
         QFileDialog D; //factory
 
         //Creer un dialoque pour abtenir le nom du fichier
         auto fileName = D.getSaveFileName();
 
         //Changer le nom du fichier courant
         currentFile = new QString(fileName);
 
         //Mettre à jour le titre de la fenêtre
         setWindowTitle(*currentFile);
     }
 
     //2. Sauvegarder le contrenu
     saveAs(*currentFile);
 
     //show the file in recentFileMenu
     if(count == 0)
     {
         recentFileActions[count]->setText(*currentFile);
         recentFileActions[count]->setVisible(true);
         previousFile= currentFile;
         count++;
     }
     else if(*previousFile!= *currentFile && count < 5)
     {
         recentFileActions[count]->setText(*currentFile);
         recentFileActions[count]->setVisible(true);
         previousFile = currentFile;
         count++;
     }
     if(count > 5)
             count = 0;
 }
 
 
 void SpreadSheet::openRecentFile()
 {
    previousFile::newFileSlot();
     QAction *action = dynamic_cast<QAction*>(sender());
     if (action){
         loadContent(action->text());
     }
 }
 
 void SpreadSheet::cutSlot(){
 
     //get the last child widget which has been focused and cast it as lie edit
     QLineEdit *lineEdit = dynamic_cast<QLineEdit*>(focusWidget());
     if(lineEdit)
     {
         //it was a line Edit
         lineEdit->cut();
     }
 }
 
 void SpreadSheet::copySlot()
 {
     //get the last child widget which has been focused and cast it as lie edit
     QLineEdit *lineEdit = dynamic_cast<QLineEdit*>(focusWidget());
     if(lineEdit)
     {
         //it was a line Edit
         lineEdit->copy();
     }
 }
 
 void SpreadSheet::pasteSlot()
 {
     //get the last child widget which has been focused and cast it as lie edit
     QLineEdit *lineEdit = dynamic_cast<QLineEdit*>(focusWidget());
     if(lineEdit)
     {
         //it was a line Edit
         lineEdit->paste();
     }
 }
 
 void SpreadSheet::findTextSlot()
 {
     FindDialog dialog;
     auto reply = dialog.exec();
     auto found = false;
 
     if(reply == FindDialog::Accepted)
     {
         QString text = dialog.getText();
 
         for(auto i=0; i<spreadsheet->rowCount(); i++)
         {
             for(auto j=0; j<spreadsheet->columnCount(); j++)
             {
                 if(spreadsheet->item(i,j))
                 {
                      if(spreadsheet->item(i,j)->text() == text){
                          spreadsheet->setCurrentCell(i,j);
                          found = true;
                      }
                 }
             }
         }
 
         if(!found)
             QMessageBox::information(this,"Error", "Not found");
     }
 }
 
 void SpreadSheet::goCellSlot()
 {
     //1. Créer le dialogue
     GoDialog D;
 
     //2.Exécuter le dialogue
     auto reply = D.exec();
 
     //3.Verifier si le dialogue a été accepté
     if(reply == GoDialog::Accepted)
     {
         //B32
         //Extraire le texte
         QString cell = D.getCell();
 
         //a->b->c ------z .......A->b->....L
         //Extraire la ligne
         int row = cell[0].toLatin1() - 'A';
 
         //Extraire le colonne
         cell = cell.remove(0,1);
         int col = cell.toInt()-1;
 
         //Changer la cellule
         statusBar()->showMessage("Changing the current cell",2000);
         spreadsheet->setCurrentCell(row,col);
     }
 }
 
 void SpreadSheet::updateStatusBar(int row,int col)
 {
     QString cell{"(%0, %1)"};
     cellLocation->setText(cell.arg(row+1).arg(col+1));
     statusBar()->showMessage("Opening File", 5000);
 }
 
 void SpreadSheet::aboutQtSlot()
 {
     QMessageBox::aboutQt(this, "About spreadsheet ");
 }
 
 


```


   <h3 style="color:#088A85;" id="Calcul-id" >go dialog</h3>


<h5 style="color:#FF8000"> godialog.cpp :</h5>

```c++
#include "godialog.h"
#include "ui_godialog.h"
#include <QRegExp>
#include <QRegExpValidator>


GoDialog::GoDialog(QWidget *parent) :
    QDialog(parent),
    ui(new Ui::GoDialog)
{
    ui->setupUi(this);

    //Epression reguliere
    QRegExp exp("[A-Z][1-9][0-9]{0,2}");

    //Valider l'expression régulière
    ui->lineEdit->setValidator(new QRegExpValidator(exp));
}

GoDialog::~GoDialog()
{
    delete ui;
}

QString GoDialog::getCell() const
{
    return ui->lineEdit->text();
}

```




 <h5 style="color:#FF8000"> finddialog.h</h5>

```c++
#ifndef FINDDIALOG_H
#define FINDDIALOG_H

#include <QDialog>

class FindDialog : public QDialog
{

public:
    explicit FindDialog(QWidget *parent = nullptr);
    ~FindDialog();
    QString getText()const;

private:
   Ui::FindDialog *ui;

};

#endif // FINDDIALOG_H


```




 <h5 style="color:#FF8000"> finddialog.cpp</h5>

```c++

#include "finddialog.h"
#include "ui_finddialog.h"

FindDialog::FindDialog(QWidget *parent) :
    QDialog(parent),
    ui(new Ui::FindDialog)
{
    ui->setupUi(this);
}

FindDialog::~FindDialog()
{
    delete ui;
}

QString FindDialog :: getText() const
{
    return ui->lineEdit->text();
}


```

<h5 style="color:#FF8000">result :</h5>

![spreedscreen](https://user-images.githubusercontent.com/86807424/146686602-0b7a35a6-3d98-46f6-b239-d70599bb5c98.png)


------------
<h3 style="color:#088A85"; > Wrap up </h3>
  
  
>In this report we created main windows SpreadSheet using Qt , complete with menus, toolbars, status bar...
and  mainwindow text editor program built around QPlainText.

--------
 
<h4 style="background-color:#F6CEF5" > By: OUMKOULTHOUME BOUNOUA </h4>
  
  </div>
