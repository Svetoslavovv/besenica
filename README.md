import javax.swing.*;
import java.awt.*;
import java.util.HashSet;
import java.util.Random;
import java.util.Set;

public class HangmanSwing extends JFrame {

    private final String[] words = {
            "КОМПЮТЪР",
            "ПРОГРАМИРАНЕ",
            "УЧИЛИЩЕ",
            "МАТЕМАТИКА",
            "ФИЗИКА",
            "БЪЛГАРИЯ",
            "ПЛОВДИВ",
            "СОФТУЕР",
            "КЛАВИАТУРА",
            "МОНИТОР",
            "ТЕХНОЛОГИЯ",
            "ПРИЛОЖЕНИЕ",
            "ИНТЕРНЕТ",
            "УЧЕНИК",
            "УЧИТЕЛ"
    };

    private String secretWord;
    private Set<Character> guessedLetters;
    private int mistakes;

    private JLabel wordLabel;
    private JLabel infoLabel;
    private JTextField inputField;
    private HangmanPanel drawingPanel;

    public HangmanSwing() {
        setTitle("Бесеница");
        setSize(600, 500);
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setLocationRelativeTo(null);

        initializeGame();

        drawingPanel = new HangmanPanel();

        wordLabel = new JLabel(getHiddenWord(), SwingConstants.CENTER);
        wordLabel.setFont(new Font("Arial", Font.BOLD, 30));

        infoLabel = new JLabel("Въведи буква:", SwingConstants.CENTER);

        inputField = new JTextField();
        JButton guessButton = new JButton("Познай");

        guessButton.addActionListener(e -> checkLetter());

        JPanel bottomPanel = new JPanel(new BorderLayout());
        bottomPanel.add(inputField, BorderLayout.CENTER);
        bottomPanel.add(guessButton, BorderLayout.EAST);

        setLayout(new BorderLayout());
        add(wordLabel, BorderLayout.NORTH);
        add(drawingPanel, BorderLayout.CENTER);

        JPanel southPanel = new JPanel(new BorderLayout());
        southPanel.add(infoLabel, BorderLayout.NORTH);
        southPanel.add(bottomPanel, BorderLayout.SOUTH);

        add(southPanel, BorderLayout.SOUTH);
    }

    private void initializeGame() {
        Random random = new Random();
        secretWord = words[random.nextInt(words.length)];
        guessedLetters = new HashSet<>();
        mistakes = 0;
    }

    private void checkLetter() {
        String text = inputField.getText().trim().toUpperCase();

        if (text.length() != 1) {
            JOptionPane.showMessageDialog(this,
                    "Въведи една буква!");
            return;
        }

        char letter = text.charAt(0);

        if (!Character.isLetter(letter)) {
            JOptionPane.showMessageDialog(this,
                    "Въведи валидна буква!");
            return;
        }

        if (guessedLetters.contains(letter)) {
            JOptionPane.showMessageDialog(this,
                    "Тази буква вече е използвана!");
            return;
        }

        guessedLetters.add(letter);

        if (!secretWord.contains(String.valueOf(letter))) {
            mistakes++;
            drawingPanel.repaint();
        }

        wordLabel.setText(getHiddenWord());

        if (isWordGuessed()) {
            JOptionPane.showMessageDialog(this,
                    "Поздравления! Позна думата: " + secretWord);
            restartGame();
            return;
        }

        if (mistakes >= 6) {
            JOptionPane.showMessageDialog(this,
                    "Загуби! Думата беше: " + secretWord);
            restartGame();
        }

        inputField.setText("");
    }

    private String getHiddenWord() {
        StringBuilder sb = new StringBuilder();

        for (char c : secretWord.toCharArray()) {
            if (guessedLetters.contains(c)) {
                sb.append(c).append(" ");
            } else {
                sb.append("_ ");
            }
        }

        return sb.toString();
    }

    private boolean isWordGuessed() {
        for (char c : secretWord.toCharArray()) {
            if (!guessedLetters.contains(c)) {
                return false;
            }
        }
        return true;
    }

    private void restartGame() {
        initializeGame();
        wordLabel.setText(getHiddenWord());
        drawingPanel.repaint();
    }

    class HangmanPanel extends JPanel {

        @Override
        protected void paintComponent(Graphics g) {
            super.paintComponent(g);

            Graphics2D g2 = (Graphics2D) g;

            g2.drawLine(100, 300, 200, 300);
            g2.drawLine(150, 300, 150, 50);
            g2.drawLine(150, 50, 300, 50);
            g2.drawLine(300, 50, 300, 80);

            if (mistakes >= 1)
                g2.drawOval(275, 80, 50, 50);

            if (mistakes >= 2)
                g2.drawLine(300, 130, 300, 220);

            if (mistakes >= 3)
                g2.drawLine(300, 150, 260, 190);

            if (mistakes >= 4)
                g2.drawLine(300, 150, 340, 190);

            if (mistakes >= 5)
                g2.drawLine(300, 220, 260, 270);

            if (mistakes >= 6)
                g2.drawLine(300, 220, 340, 270);
        }
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> {
            new HangmanSwing().setVisible(true);
        });
    }
}