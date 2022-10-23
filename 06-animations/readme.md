# 1. Introduction

## FPS: frames per seconds

- 애니메이션은 마치 사진을 찍는 것과 비슷하다. 움직이고 사진찍고, 움직이고 사진찍고 …
- FPS가 다르면 애니메이션이 다르게 보인다.

  만약 한 프레임 당 10을 이동한다고 하면

    - FPS 가 크면 더 빠르게 이동
    - FPS 가 작으면 더 느리게 이동

  → 같게 애니메이션이 되도록 방법을 찾아야 함!


# 2. Using requestAnimationFrame

## requestAnimationFrame

- 다음 frame을 호출하는 함수
- **애니메이션을 만들어주는 함수가 아님에 주의**
    - 한 번만 호출됨
    - 애니메이션을 만들기 위해서는 각 프레임마다 호출해주어야 함
    - 60 FPS 라면 1초에 60번 호출됨

```jsx
// Animation
const tick = () => {
    window.requestAnimationFrame(tick); // 호출 아님. callbackFn 넘기기
}

tick(); // 초기에 한 번 호출해주어야 함
```

## ****Adaptation to the framerate****

- 하지만 FPS 마다 animation의 속도가 다른 것이 문제

### Date

→ Date 를 사용해서 각 사람마다 동일한 속도를 내도록 하자.

```jsx
// Time
let time = Date.now();

// Animation
const tick = () => {
    const currentTime = Date.now();
    const deltaTime = currentTime - time;
    time = currentTime;

    // Update objects
    mesh.rotation.x += 0.001 * deltaTime;

    // Render
    renderer.render(scene, camera)

    window.requestAnimationFrame(tick);
}
```

- deltaTime: 현재 timestamp 에서 이전 timestamp 를 뺀 값
    - FPS 가 클수록 deltaTime 이 작다.
- deltaTime은 FPS 와 반비례하므로 변화율에 곱해서 적용하면 FPS에 관계없이 동일한 속도의 애니메이션을 적용할 수 있다.

# 3. Using Clock

- Three.js 에 내장되어 있는 것
- 0에서 시작해서 매 초당 1씩 증가함

아래는 1초에 한 바퀴 도는 예제

```jsx
// Clock
 const clock = new THREE.Clock();

// Animation
const tick = () => {
    // Clock
    const elapsedTime = clock.getElapsedTime();

    // Update objects 1: 1초에 한 바퀴 돌기
    mesh.rotation.x = elapsedTime * Math.PI * 2;

		// Update objects 2: 원 운동
    mesh.position.y = Math.sin(elapsedTime);
    mesh.position.x = Math.cos(elapsedTime);

		// Update objects 3: 카메라 움직임 + 물체 보기
    camera.position.y = Math.sin(elapsedTime);
    camera.position.x = Math.cos(elapsedTime);
    camera.lookAt(mesh.position);

    // Render
    renderer.render(scene, camera)

    window.requestAnimationFrame(tick);
}

tick();
```

### getDelta

- `getDelta` 는 원하지 않는 애니메이션이 나올 수 있으므로 웬만하면 쓰지 말자.

# 4. Using a library

## GSAP

- 애니메이션을 도와주는 라이브러리
- 동시 애니메이션 등을 처리하는 데에 유용하다.

### gsap.to()

<aside>
💡 *`function to*(targets: *TweenTarget*, vars: *TweenVars*): *core*.Tween;`

</aside>

[Docs](https://greensock.com/docs/v3/GSAP/gsap.to())

- 첫 번째는 애니메이션을 적용할 object
- 두 번째는 애니메이션을 적용할 destination

- 예시

```jsx
import gsap from 'gsap';

gsap.to(mesh.position, {
    duration: 1,
    delay: 1,
    x: 2
})
gsap.to(mesh.position, {
    duration: 1,
    delay: 2,
    x: 0
})

// Animation
const tick = () => {
    // Render
    renderer.render(scene, camera)

    window.requestAnimationFrame(tick);
}

tick();
```