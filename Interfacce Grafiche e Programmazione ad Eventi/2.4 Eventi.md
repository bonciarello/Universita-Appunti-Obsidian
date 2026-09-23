---
aliases: [Evento, Evente]
tags: [interfacce-grafiche-e-programmazione-ad-eventi]
---
La gestione degli eventi è quel meccanismo con cui un'applicazione Java può reagire ad un'azione compiuta da un utente quando interagisce con i controlli grafici, oppure ad un'azione compiuta da altri componenti software o dal sistema stesso.

## Gestione degli eventi

Il meccanismo che utilizza Java per gestire degli eventi è chiamato **delegation event model**. È caratterizzato da tre componenti:
- **event source:** è la sorgente che fa scaturire l'evento a seguito di un'azione compiuta da un utente. Ad esempio, un bottone;
- **event object:** è l'oggetto dell'evento che si è verificato e contiene i metodi che consentono di ottenere informazioni sull'evento;
- **event listener:** è l'oggetto che rimane in attesa dell'evento e che si occuperà di gestirlo in modo opportuno. Ogni listener è rappresentato da un'interfaccia.

## Action Event

È un evento che si genera quando su un componente si è verificato un evento specifico come il click del mouse o la pressione del tasto invio.

```java
JButton submit = new JButton("submit");
submit.addActionListener(new ActionListener() {
    @Override
    public void actionPerformed(ActionEvent e) {
        System.out.println("Action performed!");
    }
});
```

## MouseEvent

È un evento che si genera quando su un componente avviene un'azione del mouse, come il click, il movimento, il rilascio, ecc.

```java
JButton submit = new JButton("submit");
submit.addMouseListener(new MouseListener() {
    public void mouseReleased(MouseEvent e) {}
    public void mousePressed(MouseEvent e) {}
    public void mouseExited(MouseEvent e) {}
    public void mouseEntered(MouseEvent e) {}
    public void mouseClicked(MouseEvent e) {}
});
```

Si può utilizzare anche il MouseAdapter che implementa, tra le altre, anche l'interfaccia MouseListener. L'implementazione è vuota, e si può usare questa classe solo per ridefinire alcuni metodi di MouseListener.

```java
JButton submit = new JButton("submit");
submit.addMouseListener(new MouseAdapter() {
    public void mousePressed(MouseEvent e) {}
});
```

## KeyEvent

È un evento che si genera quando su un componente avviene un'azione della tastiera, come la pressione o il rilascio di un tasto. Attraverso la classe KeyEvent è possibile capire il tasto che è stato premuto.

```java
JTextAreaarea=newJTextArea();
area.addKeyListener(newKeyListener(){
    public void keyTyped(KeyEvent e) {}
    public void keyReleased(KeyEvent e) {}
    public void keyPressed(KeyEvent e) {
        if(e.getKeyCode() == KeyEvent.VK_LEFT)
            System.out.println("left");
        else if(e.getKeyCode() == KeyEvent.VK_A)
            System.out.println("pressed a");
    }
});
```

Anche in questo caso si può usare KeyAdapter al posto di KeyListener.
