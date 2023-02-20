# useRef

컴포넌트의 생애주기 전체에 걸쳐서 유지되는 객체.

**즉, 컴포넌트가 없어질 때까지 동일한 객체가 유지된다.**

객체 자체가 값은 아니고, `값을 참조하기 위한 객체`. 즉, 언제든지 값을 변경할 수 있다.

상태(state)가 변경되면 해당 컴포넌트와 하위 컴포넌트를 다시 렌더링하지만,

레퍼런스 객체의 현재 값(current)이 바뀌더라도 렌더링에 영향을 주지 않는다.

주요 용도:

1. 컴포넌트가 사라질 때까지 동일한 값을 써야 하는 경우. ⇒ input 등의 ID 관리.

2. (특히 useEffect 등과 함께 쓰면서 만나게 되는) 비동기 상황에서 현재 값을 제대로 쓰고 싶은 경우.

Closure → 변수를 capture, bind를 깜빡하는 문제가 종종 일어남.


```js
import { useEffect, useRef, useState } from "react";

function Timer() {
    useEffect(()=>{
        const savedTitle = document.title;

		const id = setInterval(()=>{
			document.title = `Now: ${new Date().getTime()}`;
		},2000)

        return () => {
        clearInterval(id)
        document.title = savedTitle // 저장해둔 title로 원복해준다.
        // 결론적으로 useEffect의 return은 끝날때에 대한 처리이다.
    }
	})

    return <p>Playing</p>
}

export default function TimerControl() {
    // const ref = {
    //     value: 1,
    // }
    // ref.value = 2
    // 이런 ref를 만들고
    // const로 만든 ref 지만 ref.value의 값은 변경 가능하다. 이런 개념.
    // 각자 다른 컴포넌트들이 자신들만의 이런 객체를 갖고싶어 -> useRef

    const ref = useRef(1)
    // useState는 상태가 변경되면 해당 컴포넌트와 하위 컴포넌트를 다시 렌더링함.
    // useRef는 ref.current 값이 변해도 리렌더 안함. 
    // 다른 이유로 렌더될때만 반영됨.

    const [playing, setPlaying] = useState(false)

    const handleClick = () => {
        ref.current += 1
        // setPlaying(!playing)
    }
console.log(ref.current,'ss')
    return (
        <div>
            {/* {playing ?(
                <Timer />
            ): (
                <p>Stop</p>
            ) } */}
            <button type="button" onClick={handleClick}>
                Toggle
            </button>
            <button type="button" onClick={()=>console.log(ref.current)}>see ref</button>

            <p>{ref.current}</p>
        </div>
    )
}
```

[위 코드 레포](https://github.com/heyho00/timer-app) 
useRef 브랜치 참고
