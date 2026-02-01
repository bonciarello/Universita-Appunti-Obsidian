Nella cartella *src/sound* andremo ad elencare i file di seguito, mentre nella cartella *src/resources* troveremo la musica in formato *.wav*.

Per quanto riguarda il file *Main.java*:

```java
package sound;

import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JPanel;

public class Main {
	public static void main(String[] args) {
		JFrame f = new JFrame("Sound");
		f.setSize(600,100);
		JPanel p = new JPanel();
		JButton start = new JButton("start");
		JButton stop = new JButton("stop");
		JButton restart = new JButton("restart");
		JButton loop = new JButton("loop");
		JButton incrementVolume = new JButton("+");
		JButton reduceVolume = new JButton("-");
		p.add(start); p.add(stop);
		p.add(restart); p.add(loop);
		p.add(incrementVolume);
		p.add(reduceVolume);
		f.add(p);
		Sound s = new Sound("test.wav");
		start.addActionListener(new ActionListener() {
			public void actionPerformed(ActionEvent e) {
				s.start();
			}
		});
		stop.addActionListener(new ActionListener() {
			public void actionPerformed(ActionEvent e) {
				s.stop();
			}
		});
		restart.addActionListener(new ActionListener() {
			public void actionPerformed(ActionEvent e) {
				s.restart();
			}
		});
		loop.addActionListener(new ActionListener() {
			public void actionPerformed(ActionEvent e) {
				s.loop();
			}
		});
		incrementVolume.addActionListener(new ActionListener() {
			public void actionPerformed(ActionEvent e) {
				s.incrementVolume();
			}
		});
		reduceVolume.addActionListener(new ActionListener() {
			public void actionPerformed(ActionEvent e) {
				s.reduceVolume();
			}
		});
		f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
		f.setVisible(true);
	}
}
```

Per quanto riguarda il file *Sound.java*:

```java
package sound;

import java.io.IOException;

import javax.sound.sampled.AudioInputStream;
import javax.sound.sampled.AudioSystem;
import javax.sound.sampled.Clip;
import javax.sound.sampled.FloatControl;
import javax.sound.sampled.LineUnavailableException;
import javax.sound.sampled.UnsupportedAudioFileException;

public class Sound {
	private AudioInputStream audioIn;
	private Clip clip;

	public Sound(String name) {
		try {
			audioIn = AudioSystem.getAudioInputStream(getClass().getResourceAsStream("/resources/" + name));
			clip = AudioSystem.getClip();
			clip.open(audioIn);
		} catch (UnsupportedAudioFileException | IOException | LineUnavailableException e) {
			clip = null;
			e.printStackTrace();
		}
	}

	public void loop() {
		if (clip != null) {
			clip.loop(Clip.LOOP_CONTINUOUSLY);
		}
	}

	public void start() {
		if (clip != null) {
			if (clip.getFramePosition() == clip.getFrameLength())
				clip.setFramePosition(0);
			clip.start();
		}
	}

	public void stop() {
		if (clip != null) {
			clip.stop();
		}
	}

	public void restart() {
		if (clip != null) {
			clip.stop();
			clip.setFramePosition(0);
			clip.start();
		}
	}

	public void reduceVolume() {
		if (clip != null) {
			FloatControl gainControl = (FloatControl) clip.getControl(FloatControl.Type.MASTER_GAIN);
			float value = gainControl.getValue();
			value -= 1.0f;
			if (value >= gainControl.getMinimum())
				gainControl.setValue(value);
		}
	}

	public void incrementVolume() {
		if (clip != null) {
			FloatControl gainControl = (FloatControl) clip.getControl(FloatControl.Type.MASTER_GAIN);
			float value = gainControl.getValue();
			value += 1.0f;
			if (value <= gainControl.getMaximum())
				gainControl.setValue(value);
		}
	}
}
```
