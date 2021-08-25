# CH1. 튜토리얼

## 1.1 Hello, World

```Go
// gopl.io/ch1/helloworld
package main
import "fmt"
func main() {
  fmt.Println("Hello, world")
}
```

### Go 툴체인

- go run:  .go 소스파일을 컴파일하고 라이브러리와 링크 후 결과 실행 파일을 구동

```bash
$ go run helloworld.go
Hello, world
```

- go build: 추가 작업 없이 바로 실행 할 수 있는 바이너리 파일을 생성

```bash
$ go build helloworld.go
```

```bash
$ ./helloworld
Hello, world
```

- go get: 소스 파일을 가져와 해당 디렉토리에 배치 (ch 2.6, 10.7)

```bash
$ go get gopl.io/ch1/helloworld
```

### Go 프로그램

- Go 코드는 패키지로 구성되며,  패키지는 하나의 디렉토리와 이 디렉토리 안에 있는 하나 이상의 .go 소스파일로 이루어져 있다.

- 각 소스 파일은 파일이 속하는 패키지를 정의하고, import 하는 다른 패키지 목록과 파일에 저장되어 있는 프로그램의 선언 목록으로 이루어 진다.
- 누락되거나 불필요한 임포트가 있다면 컴파일 되지 않는다. (사용되지 않는 패키지의 누적을 방지)



## 1.2 커맨드라인 인수

- 커맨드라인의 인수는 `os` 패키지의 일부인 `Args`변수로 사용할 수 있다.
- `os.Args` 변수는 문자열의 슬라이스다. 
  - 슬라이스의 각 원소는 `s[i]`, 부분집합은` s[m:n]`로 접근 가능하며, 동적인 크기를 갖는 배열이다.
  - 슬라이스 원소의 개수는 `len(s)`로 알수있다.

- `os.Args[0]`은 명령 자체의 이름이고, 그 이외의 원소들은 프로그램이 실행될 때 제공된 인수다.
- 다음은 커맨드라인 인수를 한줄로 출력하는 유닉스의 echo 명령을 구현한 것이다.

```go
package main
import (
	"fmt"
	"os"
)
func main() {
	var s, sep string
	for i := 1; i < len(os.Args); i++ {
		s += sep + os.Args[i]
		sep = " "
	}
	fmt.Println(s)
}
```

- `var s, sep string`: 문자열 타입의 변수 s, sep의 선언.
  - 변수 선언 중에 초기화 할 수 있고, 초기화 되지 않은 경우에는 묵시적으로 해당 타입의 제로 값으로 초기화 된다. (숫자 : 0, 문자열: "")
- 증가 구문 `i++`은  i += 1과 같다.
  - C와 다른점은 표현식이 아닌 문장이기 때문에 `j = i++` 이 허용되지 않으며, 후치연산자만 가능하여 `--i`도 혀용되지 않는다.
- `for 루프`는 Go의 유일한 루프문이다.

```go
for 초기화; 조건; 후처리 {
  // 구문
}
// "while" 루프
for 조건 {
  ...
}
// 무한루프
for {
  ...
}
```

- `for 루프`에는 문자열이나 슬라이스 데이터 타입의 값 범위에 걸쳐 반복하는 형태도 있다.

```go
package main
import (
	"fmt"
	"os"
)
func main() {
	s, sep := "", ""
	for _, arg := range os.Args[1:] {
		s += sep + arg
		sep = " "
	}
	fmt.Println(s)
}
```

- `range`는 각 반복에서 값의 쌍(인덱스와 해당 인덱스 원소의 값)을 생성한다. 
- 이 예제에서는 인덱스를 사용하지 않고 있으며, 이런 경우에는 `_` (빈 식별자)를 사용해서 처리할 수 있다. 
  -> 이렇게 하지 않는다면 사용하지 않은 지역 변수로 인해 컴파일 에러가 발생한다.
- 변수를 선언하는 방법은 여러가지가 있다.

```go
s := ""	// 패키지 수준 변수가 아닌 함수안에서만 사용 가능
var s string	// 문자열의 기본 초기값("")에 의존하는 형식
var s = ""	// 여러 변수를 선언하는 경우가 아니라면 거의 사용되지 않음
var s string = ""	// 명시적으로 변수의 타입 선언. -> 변수 타입과 초기값의 타입이 같을 때는 중복 되므로 명시적 선언이 필요 없음.
```

- 위의 함수를 더 간단하게 사용하는 방법은 strings 패키지의 Join을 사용하는 방법이 있다.

```go
fmt.Println(strings.Join(os.Args[1:], " "))
```



## 1.3 중복 줄 찾기

```go
package main
import (
	"bufio"
	"fmt"
	"os"
)
func main() {
	counts := make(map[string]int)
	input := bufio.NewScanner(os.Stdin)
	for input.Scan() {
		counts[input.Text()]++
	}
	// NOTE: ignoring potential errors from input.Err()
	for line, n := range counts {
		if n > 1 {
			fmt.Printf("%d\t%s\n", n, line)
		}
	}
}
```

- `if`문 역시 `for`문 처럼 조건절 주위에 괄호가 사용되지는 않지만 본문에는 중괄호가 필요하다. 뒤에 else 구문이 올 수도 있다.
- `map`은 키/값 쌍을 가지고, 값의 저장과 추출, 특정 원소를 찾는 검사를 constant time동안 수행한다.
  - 키는 == 연산자로 비교될 수 있는 모든 값을 사용할 수 있고, 값은 타입 제약이 없다.
  - 내장함수 `make`는 새로운 빈 맵을 생성하고 다른 작업에서도 쓸 수 있다. (Ch 4.3)
- bufio의 Scanner타입은 줄단위로 들어오는 입력을 처리하는 가장 쉬운 방법이다.
- Printf는 개행문자가 포함되지 않았으며, %d 와 같은 formatter를 사용해 포매팅한 결과를 생성한다.

| Formatter  |                                                          |
| ---------- | -------------------------------------------------------- |
| %d         | 십진 정수                                                |
| %x, %o, %b | 16진, 8진, 2진 정수                                      |
| %f, %g, %e | 부동소수점 수: 3.141593, 3.141592653589793, 3.141593e+00 |
| %t         | Boolean: true / false                                    |
| %c         | 룬 (유니코드 문자열)                                     |
| %s         | 문자열                                                   |
| %q         | 따옴표로 묶인 문자열                                     |
| %v         | 원래 형태의 값                                           |
| %T         | 값의 타입                                                |
| %%         | % 기호                                                   |

```go
// dup2
package main

import (
	"bufio"
	"fmt"
	"os"
)

func main() {
	counts := make(map[string]int)
	files := os.Args[1:]
	if len(files) == 0 {
		countLines(os.Stdin, counts)
	} else {
		for _, arg := range files {
			f, err := os.Open(arg)
			if err != nil {
				fmt.Fprintf(os.Stderr, "dup2: %v\n", err)
				continue
			}
			countLines(f, counts)
			f.Close()
		}
	}
	for line, n := range counts {
		if n > 1 {
			fmt.Printf("%d\t%s\n", n, line)
		}
	}
}

func countLines(f *os.File, counts map[string]int) {
	input := bufio.NewScanner(f)
	for input.Scan() {
		counts[input.Text()]++
	}
	// NOTE: ignoring potential errors from input.Err()
}
```



- os.Open함수는 첫번째로 열린 파일(os.File), 두 번째로 내장된 error 타입값을 반환한다. 
- continue 구문은 루프 바깥쪽의 다음 반복으로 이동할 때 사용한다.
- 이 예제에서는 os.Open에서의 예외 처리를 해야하지만, 이 작은 가능성 은 무시한채 짜여져 있다.

```go
// dup3
package main
import (
	"fmt"
	"io/ioutil"
	"os"
	"strings"
)

func main() {
	counts := make(map[string]int)
	for _, filename := range os.Args[1:] {
		data, err := ioutil.ReadFile(filename)
		if err != nil {
			fmt.Fprintf(os.Stderr, "dup3: %v\n", err)
			continue
		}
		for _, line := range strings.Split(string(data), "\n") {
			counts[line]++
		}
	}
	for line, n := range counts {
		if n > 1 {
			fmt.Printf("%d\t%s\n", n, line)
		}
	}
}
```

- 세번째 예제는 파일만 읽고 표준 입력은 사용하지 않는 예제이다.



## 1.4 애니메이션 GIF

> Go 내장 표준 이미지 패키지 

```go
package main
import (
	"image"
	"image/color"
	"image/gif"
	"io"
	"math"
	"math/rand"
	"os"
)

var palette = []color.Color{color.White, color.Black}

const (
	whiteIndex = 0 // first color in palette
	blackIndex = 1 // next color in palette
)

func main() {
  lissajous(os.Stdout)
}
func lissajous(out io.Writer) {
	const (
		cycles  = 5     // number of complete x oscillator revolutions
		res     = 0.001 // angular resolution
		size    = 100   // image canvas covers [-size..+size]
		nframes = 64    // number of animation frames
		delay   = 8     // delay between frames in 10ms units
	)
	freq := rand.Float64() * 3.0 // relative frequency of y oscillator
	anim := gif.GIF{LoopCount: nframes}
	phase := 0.0 // phase difference
	for i := 0; i < nframes; i++ {
		rect := image.Rect(0, 0, 2*size+1, 2*size+1)
		img := image.NewPaletted(rect, palette)
		for t := 0.0; t < cycles*2*math.Pi; t += res {
			x := math.Sin(t)
			y := math.Sin(t*freq + phase)
			img.SetColorIndex(size+int(x*size+0.5), size+int(y*size+0.5),
				blackIndex)
		}
		phase += 0.1
		anim.Delay = append(anim.Delay, delay)
		anim.Image = append(anim.Image, img)
	}
	gif.EncodeAll(out, &anim) // NOTE: ignoring encoding errors
}
```

- const선언은 상수에 이름을 붙이고, 이에 따라 cycle, frame, delay 등 수치 파라미터들은 컴파일시 고정된 값을 값는다. (패키니 수준내 혹은 함수 수준내에서 사용할 수 있다. ) 
  상수의 값은 숫자, 문자열, 불리언 이어야 한다.
- `[]color.Color{..}`와 `gif.GIF{...}` 표현식은 복합 리터럴로 Go의 복합 타입을 원소값의 나열로 초기화하는 표기법이다.
- 구조체는 필드라고 불리는 값의 그룹이며, 보통 서로 다른 타입들을 하나의 객체로 묶어 단일 객체 취급이 가능하다
- 구조체의 개별 필드는 명시적으로 anim의 Delay, Image 필드를 갱신하는 마지막 구문처럼 dot notation으로 사용할 수 있다.

```bash
$ go build gopl.io/ch1/lissajous
$ ./lissajous >out.gif
```



## 1.5 URL 반입

- Go는 net 그룹의 하위 패키지로 저수준 네트워크 연결을 생성해 서버를 설정하기 쉽게 하는 패키지를 제공한다.

```go
package main
import (
	"fmt"
	"io/ioutil"
	"net/http"
	"os"
)

func main() {
	for _, url := range os.Args[1:] {
		resp, err := http.Get(url)
		if err != nil {
			fmt.Fprintf(os.Stderr, "fetch: %v\n", err)
			os.Exit(1)
		}
		b, err := ioutil.ReadAll(resp.Body)
		resp.Body.Close()
		if err != nil {
			fmt.Fprintf(os.Stderr, "fetch: reading %s: %v\n", url, err)
			os.Exit(1)
		}
		fmt.Printf("%s", b)
	}
}
```

- `net/http`의 http.Get 함수는 http 요청을 생성하고 결과를 resp로 반환한다. resp의 Body 부분에는 스트림 형태의 서버 응답이 포함되어 있다.
- `ioUtil.ReadAll()`은 전체 응답을 읽고 Body 스트림은 리소스 누출을 막기위해 Close() 된다.

- `os.Exit(1)`은 두 오류 중 어떤 오류라도 상태코드 1로 프로세스를 종료한다.

## 1.6  URL 동시 반입

> Goroutine, 채널

- Go의 가장 흥미롭고 기발한 측면 중 하나는 동시성 프로그래밍에 대한 지원이다.
- 다음 프로그램은 동시에 여러 URL에서 응답을 받아오며, 전체 처리시간은 각 응답시간의 합이 아니라 가장 오래 걸릴 때의 시간이 된다.
- 다음 예제는 결과의 크기와 응답에 걸린 시간을 출력한다.

```go
package main

import (
	"fmt"
	"io"
	"io/ioutil"
	"net/http"
	"os"
	"time"
)

func main() {
	start := time.Now()
	ch := make(chan string)
	for _, url := range os.Args[1:] {
		go fetch(url, ch) // start a goroutine
	}
	for range os.Args[1:] {
		fmt.Println(<-ch) // receive from channel ch
	}
	fmt.Printf("%.2fs elapsed\n", time.Since(start).Seconds())
}

func fetch(url string, ch chan<- string) {
	start := time.Now()
	resp, err := http.Get(url)
	if err != nil {
		ch <- fmt.Sprint(err) // send to channel ch
		return
	}

	nbytes, err := io.Copy(ioutil.Discard, resp.Body)
	resp.Body.Close() // don't leak resources
	if err != nil {
		ch <- fmt.Sprintf("while reading %s: %v", url, err)
		return
	}
	secs := time.Since(start).Seconds()
	ch <- fmt.Sprintf("%.2fs  %7d  %s", secs, nbytes, url)
}
```

- 고루틴은 함수의 동시 실행이다. 
- 채널은 한 고루틴에서 지정된 타입의 값을 다른 고루틴으로 전달하는 통신방식이다. 
- main 함수는 고루틴 안에서 실행된다.
- fetch 함수는 응답이 도착하는대로 `ch <- 표현식`으로 ch 채널에 응답 값을 보낸다.
- 각 고루틴이 독립된 단위로 처리되어 동시에 두 고루틴이 완료된 경우 출력이 섞일 위험을 사전에 방지한다.

```bash
$ go build gopl.io/ch1/fetchall
$ ./fetchall https://golang.org http://gopl.io https://godoc.org
0.14s	6853 https://godoc.org
0.16s	7261 https://golang.org
0.48s 2475 http://gopl.io
0.48s elapsed
```



## 1.7 웹 서버

```go
// server1
package main
import (
	"fmt"
	"log"
	"net/http"
)
func main() {
	http.HandleFunc("/", handler) // each request calls handler
	log.Fatal(http.ListenAndServe("localhost:8000", nil))
}
// handler echoes the Path component of the requested URL.
func handler(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintf(w, "URL.Path = %q\n", r.URL.Path)
}
```

- main 함수는 '/'로 시작하는 URL에 핸들러 함수를 연결하고 8000번 포트로 들어오는 요청을 처리하는 서버를 시작한다.
- 요청은 `http.Request`로 표현되고 그 안에는 들어온 request url을 비롯한 관련 필드가 존재한다.

```bash
$ go run src/gopl.io/ch1/server1/main/go &
$ go build gopl.io/ch1/fetch
$ ./fetch http://localhost:8000
URL.Path = "/"
$ ./fetch http://localhost:8000/help
URL.Path = "/help"
```



```go
// server2. /count 요청에 대해서는 요청된 수를 반환
package main
import (
	"fmt"
	"log"
	"net/http"
	"sync"
)
var mu sync.Mutex
var count int
func main() {
	http.HandleFunc("/", handler)
	http.HandleFunc("/count", counter)
	log.Fatal(http.ListenAndServe("localhost:8000", nil))
}

// handler echoes the Path component of the requested URL.
func handler(w http.ResponseWriter, r *http.Request) {
	mu.Lock()
	count++
	mu.Unlock()
	fmt.Fprintf(w, "URL.Path = %q\n", r.URL.Path)
}

// counter echoes the number of calls so far.
func counter(w http.ResponseWriter, r *http.Request) {
	mu.Lock()
	fmt.Fprintf(w, "Count %d\n", count)
	mu.Unlock()
}
```

- 동시에 count 갱신하려는 경우에 데이터 부결성을 보장하지 못할 수  있다.  -> race condition 이라는 심각한 버그 발생 가능
- 이를 막기 위해 한번에 하나의 고루틴만이 해당 변수에 접근해야하며 이를 위해 `mu.Lock() / mu.Unlock()`을 호출해 동시 접근을 막는다.