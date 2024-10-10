### Informações importantes

1. Quando mudamos um estado no componente Pai, o componente filho não será recriado, se não tiver alguma ligação específica;
2. Para que o componente filho seja recriado, podemos usar a "gambiarra" de colocar uma key nele, mesmo não sendo uma lista a ser renderizada, pois com isso,
   sempre que o valor da key de um componente muda ele é recriado;

```tsx
<QuestionTimer key={activeQuestionIndex} timeout={10000} onTimeout={handleSkipAnswer} />
```

3. Sempre tomar cuidado ao passar uma função como props e associa-la a dependência de um hook, já que, sempre que um componente pai é recriado, uma nova instância da função é criada, fazendo com que o hook com essa dependência seja ativado;

```tsx
const handleSelectAnswer = useCallback((selectedAnswer) => {
    setUserAnswers((prevUserAnswers) => {
        return [...prevUserAnswers, selectedAnswer];
    });
}, []);

const handleSkipAnswer = useCallback(() => handleSelectAnswer(null), [handleSelectAnswer]);

(...)

<QuestionTimer key={activeQuestionIndex} timeout={10000} onTimeout={handleSkipAnswer} />

(...)

export default function QuestionTimer({ timeout, onTimeout }) {
    const [remainingTime, setRemainingTime] = useState(timeout);

    useEffect(() => {
        const timer = setTimeout(onTimeout, timeout);

        return () => {
        clearTimeout(timer);
        };
    }, [timeout, onTimeout]);

    (...)
}
```

4. Podemos e devos usar o useRef() para armazenar um valor que não deverá ser alterado caso o componente seja recarregado por conta de algum estado;

```tsx
const shuffledAnswers = useRef();

(...)

if (!shuffledAnswers.current) {
    // O embaralhamento das questões precisa ir depois para o array na estourar
    shuffledAnswers.current = [...QUESTIONS[activeQuestionIndex].answers];
    shuffledAnswers.current.sort((a, b) => Math.random() - 0.5);
}
```
