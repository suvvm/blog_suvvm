---
title: Java断点续传（基于socket与RandomAccessFile的简单实现）
date: 2019-05-09 16:58:39
categories: 
- Java
tags:
- 功能实现
- socket
---
<meta name="referrer" content="no-referrer"/>
　　这是一个简单的C/S架构实现断点续传的方法，基本实现思路是将服务器注册至某个空闲端口用来监视并处理每个客户端的传输请求。

　　客户端先获得用户给予的需传输文件与目标路径，之后根据该文件实例化RandomAccessFile为只读，之后客户端向服务器发送需传输的**文件名**文件大小与目标路径，服务器没接收到一个客户端的请求就会建立一个新的线程去处理它，根据接收到的文件名到目标路径中去寻找目标路径中是否已经有该文件名的.temp临时文件（如果没有就创建它），之后服务器会将文件已经传输的大小（临时文件大小）返回给客户端（例如临时文件刚刚建立返回的便是0），客户端会将刚刚建立的RandomAccessFile对象的文件指针指向服务器返回的位置，之后以1kb为一组向服务器传输需传输文件的内容数据，服务器则接收数据并将其写入临时文件中，并根据现有数据画出进度条。在文件传输完毕后客户端会将临时文件重命名为最初接收到的文件名。

服务器代码： 

```c++\
import java.awt.Color;
import java.awt.Container;
import java.awt.Dimension;
import java.awt.FlowLayout;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.io.File;
import java.io.IOException;
import java.io.RandomAccessFile;
import java.net.ServerSocket;
import java.net.Socket;

import javax.swing.BoxLayout;
import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JOptionPane;
import javax.swing.JPanel;
import javax.swing.JProgressBar;
 
public class FileTransferServer extends ServerSocket {
 
    private static final int SERVER_PORT = 8899; // 服务端端口
 
    public FileTransferServer() throws Exception {
        super(SERVER_PORT);
    }
 
    public void load() throws Exception {
        while (true) {
            // server尝试接收其他Socket的连接请求，server的accept方法是阻塞式的
            Socket socket = this.accept();
           
            // 每接收到一个Socket就建立一个新的线程来处理它
            new Thread(new Task(socket)).start();
        }
    }
     //处理客户端传输过来的文件线程类
    class Task implements Runnable {
 
        private Socket socket;
        private DataInputStream dis;
        private DataOutputStream dos;
        private RandomAccessFile rad;
        private JFrame frame;    //用来显示进度条
        private Container contentPanel;
        private JProgressBar progressbar;
        private JLabel label;
            
        public Task(Socket socket) {
            frame = new JFrame("文件传输");
            this.socket = socket;
        }
 
        @Override
        public void run() {
            try {
                dis = new DataInputStream(socket.getInputStream());
                dos = new DataOutputStream(socket.getOutputStream());
                String targetPath = dis.readUTF();    //接收目标路径
                String fileName = dis.readUTF();    //接收文件名
                //System.out.println("服务器：接收文件名");
                long fileLength = dis.readLong();    //接收文件长度
                //System.out.println("服务器：接收文件长度");
                File directory = new File(targetPath);    //目标地址
                if(!directory.exists()) {    //目标地址文件夹不存在则创建该文件夹
                    directory.mkdir();
                }
                File file = new File(directory.getAbsolutePath() + File.separatorChar + fileName + ".temp");    //建立临时数据文件.temp
                //System.out.println("服务器：加载temp文件");
                rad = new RandomAccessFile(directory.getAbsolutePath() + File.separatorChar + fileName + ".temp", "rw");
                long size = 0;
                if(file.exists() && file.isFile()){    //如果目标路径存在且是文件，则获取文件大小
                    size = file.length();
                }
                //System.out.println("服务器：获的当前已接收长度");
                dos.writeLong(size);    //向客户端发送当前数据文件大小
                dos.flush();
                //System.out.println("服务器：发送当前以接收文件长度");
                int barSize = (int)(fileLength / 1024);    //进度条当前进度
                int barOffset = (int)(size / 1024);        //进度条总长
                frame.setSize(300,120); //传输界面
                contentPanel = frame.getContentPane();
                contentPanel.setLayout(new BoxLayout(contentPanel, BoxLayout.Y_AXIS));
                progressbar = new JProgressBar();    //进度条
                label = new JLabel(fileName + " 接收中");
                contentPanel.add(label);
                progressbar.setOrientation(JProgressBar.HORIZONTAL);    //进度条为水平
                progressbar.setMinimum(0);    //进度条最小值
                progressbar.setMaximum(barSize);    //进度条最大值
                progressbar.setValue(barOffset);    //进度条当前值
                progressbar.setStringPainted(true); //显示进度条信息
                progressbar.setPreferredSize(new Dimension(150, 20));    //进度条大小
                progressbar.setBorderPainted(true);    //为进度条绘制边框
                progressbar.setBackground(Color.pink);    //进度条颜色为骚粉
                JButton cancel = new JButton("取消");    //取消按钮
                JPanel barPanel = new JPanel();
                barPanel.setLayout(new FlowLayout(FlowLayout.LEFT));
                barPanel.add(progressbar);
                barPanel.add(cancel);
                contentPanel.add(barPanel);
                cancel.addActionListener(new cancelActionListener());
                //为取消按钮注册监听器
                frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
                frame.setVisible(true);
                rad.seek(size);    //移动文件指针
                //System.out.println("服务器：文件定位完成");
                int length;
                byte[] bytes=new byte[1024];
                while((length = dis.read(bytes, 0, bytes.length)) != -1){
                    rad.write(bytes,0, length);    //写入文件
                    progressbar.setValue(++barOffset);    //更新进度条（由于进度条每个单位代表大小为1kb，所以太小的文件就显示不出啦）
                }
                if (barOffset >= barSize) {    //传输完成后的重命名
                    if(rad != null)
                         rad.close();
                    if(!file.renameTo(new File(directory.getAbsolutePath() + File.separatorChar + fileName))) {
                        file.delete();
                        //防御性处理删除临时文件
                    }
                    //System.out.println("服务器：临时文件重命名完成");
                }        
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                try {    //关闭资源
                    if(rad != null)
                         rad.close();
                    if(dis != null)
                        dis.close();
                    if(dos != null)
                        dos.close();
                    frame.dispose();
                    socket.close();
                } catch (Exception e) {}
            }
        }
        class cancelActionListener implements ActionListener{    //取消按钮监听器
            public void actionPerformed(ActionEvent e){
                try {
                    //System.out.println("服务器：接收取消");
                    if(dis != null)
                        dis.close();
                    if(dos != null)
                        dos.close();
                    if(rad != null)
                        rad.close();
                    frame.dispose();
                    socket.close();
                    JOptionPane.showMessageDialog(frame, "已取消接收，连接关闭！", "提示：", JOptionPane.INFORMATION_MESSAGE);    
                    label.setText(" 取消接收,连接关闭");
                } catch (IOException e1) {
                    
                }
            }
        }
    }  
}
```

 客户端代码： 

```c++
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.io.File;
import java.io.IOException;
import java.io.RandomAccessFile;
import java.net.Socket; 

public class FileTransferClient extends Socket {
 
    private static final String SERVER_IP = "127.0.0.1"; // 服务端IP
    private static final int SERVER_PORT = 8899; // 服务端端口
    private Socket client;
    private DataOutputStream dos;
    private DataInputStream dis;  
    private RandomAccessFile rad;  

    public FileTransferClient() throws Exception {
        super(SERVER_IP, SERVER_PORT);
        this.client = this;
        //System.out.println("客户端：成功连接服务端");
    }
    
    public void sendFile(String filePath, String targetPath) throws Exception {
        try {
            File file = new File(filePath);
            
            if(file.exists()) {
                dos = new DataOutputStream(client.getOutputStream());     //发送信息 getOutputStream方法会返回一个java.io.OutputStream对象
                dis = new DataInputStream(client.getInputStream());    //接收远程对象发送来的信息  getInputStream方法会返回一个java.io.InputStream对象
                dos.writeUTF(targetPath); //发送目标路径
                dos.writeUTF(file.getName()); //发送文件名
                //System.out.println("客户端：发送文件名");
                rad = new RandomAccessFile(file.getPath(), "r");
                /*
                 * RandomAccessFile是Java输入输出流体系中功能最丰富的文件内容访问类，既可以读取文件内容，也可以向文件输出数据。
                 * 与普通的输入/输出流不同的是，RandomAccessFile支持跳到文件任意位置读写数据，RandomAccessFile对象包含一个记录指针，用以标识当前读写处的位置。
                 * 当程序创建一个新的RandomAccessFile对象时，该对象的文件记录指针对于文件头 r代表读取
                 */
                dos.flush();    //作用见下方介绍
                dos.writeLong(file.length()); //发送文件长度
                //System.out.println("客户端：发送文件长度");
                dos.flush();
                long size = dis.readLong();    //读取当前已发送文件长度
                //System.out.println("客户端：开始传输文件 ");
                int length = 0;
                byte[] bytes = new byte[1024];    //每1kb发送一次
                if (size < rad.length()) {
                    rad.seek(size);
                    //System.out.println("客户端：文件定位完成");
                    //移动文件指针
                    while((length = rad.read(bytes)) > 0){
                        dos.write(bytes, 0, length);                            
                        dos.flush();
                        //每1kb清空一次缓冲区
                        //为了避免每读入一个字节都写一次，java的输流有了缓冲区，读入数据时会首先将数据读入缓冲区，等缓冲区满后或执行flush或close时一次性进行写入操作
                    }
                }
                //System.out.println("客户端：文件传输成功 ");
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {    //关闭资源
            if(dos != null)
                dos.close();
            if(dis != null)
                dis.close();
            if(rad != null)
                rad.close();
            client.close();
        }
        
    }
 
    class cancelActionListener implements ActionListener{    //关闭按钮监听器
        public void actionPerformed(ActionEvent e3){
            try {
                //System.out.println("客户端：文件传输取消");
                if(dis != null)
                    dis.close();
                if(dos != null)
                    dos.close();
                if(rad != null)
                    rad.close();
                client.close();
            } catch (IOException e1) {
                
            }
        }
    }   
}
```

　　传输文件是一个耗时操作，若直接实例化客户端对服务器发送数据会造成UI假死的情况，直到文件传输完成后才会恢复，所以建议在实例化客户端时单独建立一个新线程。

测试代码：

```c++
import javax.swing.JFrame;
import javax.swing.JButton;
import javax.swing.JFileChooser;
import java.awt.event.ActionListener;
import java.awt.event.MouseAdapter;
import java.awt.event.MouseEvent;
import java.awt.event.ActionEvent;

public class MainFrame extends JFrame{
    public MainFrame() {
        this.setSize(1280, 768);
        getContentPane().setLayout(null);
        
        JButton btnNewButton = new JButton("传输文件");    //点击按钮进行文件传输
        btnNewButton.addMouseListener(new MouseAdapter() {
            @Override
            public void mouseClicked(MouseEvent e) {
                // TODO 自动生成的方法存根
                super.mouseClicked(e);
                JFileChooser fileChooser = new JFileChooser();    //fileChooser用来选择要传输的文件
                fileChooser.setDialogTitle("选择要传输的文件");
                int stFile = fileChooser.showOpenDialog(null);
                if(stFile == fileChooser.APPROVE_OPTION){    //选择了文件
                    JFileChooser targetPathChooser = new JFileChooser();    //targetPathChooser用来选择目标路径
                    targetPathChooser.setDialogTitle("选择目标路径");
                    targetPathChooser.setFileSelectionMode(JFileChooser.DIRECTORIES_ONLY);    //只能选择路径
                    int stPath = targetPathChooser.showOpenDialog(null);
                    if(stPath == targetPathChooser.APPROVE_OPTION) {    //选择了路径
                        //新建一个线程实例化客户端
                        new Thread(new NewClient( fileChooser.getSelectedFile().getPath(), targetPathChooser.getSelectedFile().getPath())).start();
                    }
                }
            }
        });
        btnNewButton.setBounds(526, 264, 237, 126);
        getContentPane().add(btnNewButton);
    }
    class NewClient implements Runnable {    //用于实例化客户端的线程
        private String fileP;    //需复制文件路径
        private String targetP;    //目标路径
        public NewClient(String fileP, String targetP) {    //构造函数
            this.fileP = fileP;
            this.targetP = targetP;
        }
        @Override
        public void run() {
            // TODO 自动生成的方法存根
            try {
                @SuppressWarnings("resource")
                FileTransferClient ftc = new FileTransferClient();
                //实例化客户端
                ftc.sendFile(fileP, targetP);
            } catch (Exception e1) {
                // TODO 自动生成的 catch 块
                e1.printStackTrace();
            }
        }
    }
    public static void main(String[] args) {
        // TODO 自动生成的方法存根
        MainFrame mainFrame = new MainFrame();
        mainFrame.setVisible(true);
        mainFrame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        try {
             @SuppressWarnings("resource")
            FileTransferServer server = new FileTransferServer(); // 启动服务端
             server.load();
        } catch (Exception e) {
              e.printStackTrace();
        }
    }
}
```

　　演示：

　　1运行MainFame

　　2点击传输文件

![img](https://img2018.cnblogs.com/blog/1447131/201905/1447131-20190509164942859-643737801.png)

　　3选择要传输的文件

![img](https://img2018.cnblogs.com/blog/1447131/201905/1447131-20190509165100003-685598377.png)

　　4选择目标路径

![img](https://img2018.cnblogs.com/blog/1447131/201905/1447131-20190509165148338-1675421586.png)

　　5点击打开

![img](https://img2018.cnblogs.com/blog/1447131/201905/1447131-20190509165245218-844234119.png)

　　点击取消

　　之后重复2 - 5的操作。

![img](https://img2018.cnblogs.com/blog/1447131/201905/1447131-20190509165509605-1954857083.png)

　　啊我手速慢，问题不大，你会发现断点续传已经实现了。