# Game-for-java-by-Oajdeept
import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.KeyEvent;
import java.awt.event.KeyListener;
import java.util.ArrayList;
import java.util.List;

public class EnhancedBrickBreaker extends JPanel implements KeyListener, ActionListener {
    private Timer timer;
    private int delay = 8;
    private int playerX = 310;
    private int ballPosX = 120; 
    private int ballPosY = 350;
    private int ballDirX = -1;
    private int ballDirY = -2;
    private List<Rectangle> bricks;
    private int lives = 3;
    private int score = 0;
    private int level = 1;

    public EnhancedBrickBreaker() {
        initializeBricks();
        addKeyListener(this);
        setFocusable(true);
        setFocusTraversalKeysEnabled(false);
        timer = new Timer(delay, this);
        timer.start();
    }

    private void initializeBricks() {
        bricks = new ArrayList<>();
        for (int i = 0; i < 5; i++) {
            for (int j = 0; j < 10; j++) {
                bricks.add(new Rectangle(j * 70 + 50, i * 30 + 50, 60, 20));
            }
        }
    }

    @Override
    public void paint(Graphics g) {
        // Background
        g.setColor(Color.black);
        g.fillRect(1, 1, 692, 592);

        // Borders
        g.setColor(Color.yellow);
        g.fillRect(0, 0, 3, 592);
        g.fillRect(0, 0, 692, 3);
        g.fillRect(691, 0, 3, 592);

        // Paddle
        g.setColor(Color.green);
        g.fillRect(playerX, 550, 100, 8);

        // Ball
        g.setColor(Color.yellow);
        g.fillOval(ballPosX, ballPosY, 20, 20);

        // Bricks
        g.setColor(Color.red);
        for (Rectangle brick : bricks) {
            g.fillRect(brick.x, brick.y, brick.width, brick.height);
        }

        // Score
        g.setColor(Color.white);
        g.setFont(new Font("Arial", Font.PLAIN, 20));
        g.drawString("Score: " + score, 580, 30);

        // Lives
        g.drawString("Lives: " + lives, 50, 30);

        // Level
        g.drawString("Level: " + level, 320, 30);

        g.dispose();
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        timer.start();
        repaint();
        move();
    }

    private void move() {
        ballPosX += ballDirX;
        ballPosY += ballDirY;

        // Ball collision with walls
        if (ballPosX < 0 || ballPosX > 670) {
            ballDirX = -ballDirX;
        }
        if (ballPosY < 0) {
            ballDirY = -ballDirY;
        }

        // Ball collision with paddle
        if (new Rectangle(ballPosX, ballPosY, 20, 20).intersects(new Rectangle(playerX, 550, 100, 8))) {
            ballDirY = -ballDirY;
        }

        // Ball collision with bricks
        for (int i = 0; i < bricks.size(); i++) {
            Rectangle brick = bricks.get(i);
            if (new Rectangle(ballPosX, ballPosY, 20, 20).intersects(brick)) {
                bricks.remove(brick);
                ballDirY = -ballDirY;
                score += 10;
                break;
            }
        }

        // Ball falls down
        if (ballPosY > 570) {
            lives--;
            if (lives == 0) {
                timer.stop();
                int response = JOptionPane.showConfirmDialog(this, "Game Over! Would you like to play again?", "Game Over", JOptionPane.YES_NO_OPTION);
                if (response == JOptionPane.YES_OPTION) {
                    restartGame();
                } else {
                    System.exit(0);
                }
            } else {
                resetBallAndPaddle();
            }
        }

        // Check for level up
        if (bricks.isEmpty()) {
            levelUp();
        }
    }

    private void resetBallAndPaddle() {
        ballPosX = 120;
        ballPosY = 350;
        ballDirX = -1;
        ballDirY = -2;
        playerX = 310;
    }

    private void levelUp() {
        level++;
        delay--;
        timer.setDelay(delay);
        initializeBricks();
        resetBallAndPaddle();
        timer.start();
    }

    private void restartGame() {
        lives = 3;
        score = 0;
        level = 1;
        delay = 8;
        initializeBricks();
        resetBallAndPaddle();
        timer.start();
    }

    @Override
    public void keyPressed(KeyEvent e) {
        if (e.getKeyCode() == KeyEvent.VK_RIGHT) {
            if (playerX >= 600) {
                playerX = 600;
            } else {
                playerX += 20;
            }
        }
        if (e.getKeyCode() == KeyEvent.VK_LEFT) {
            if (playerX <= 10) {
                playerX = 10;
            } else {
                playerX -= 20;
            }
        }
    }

    @Override
    public void keyReleased(KeyEvent e) { }

    @Override
    public void keyTyped(KeyEvent e) { }

    public static void main(String[] args) {
        JFrame frame = new JFrame();
        EnhancedBrickBreaker game = new EnhancedBrickBreaker();
        frame.setBounds(10, 10, 700, 600);
        frame.setTitle("Brick Breaker");
        frame.setResizable(false);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.add(game);
        frame.setVisible(true);
    }
}
