# react-find-sum-game
A simple game in react

    var possibleCombinationSum = function(arr, n) {
      if (arr.indexOf(n) >= 0) { return true; }
      if (arr[0] > n) { return false; }
      if (arr[arr.length - 1] > n) {
        arr.pop();
        return possibleCombinationSum(arr, n);
      }
      var listSize = arr.length, combinationsCount = (1 << listSize)
      for (var i = 1; i < combinationsCount ; i++ ) {
        var combinationSum = 0;
        for (var j=0 ; j < listSize ; j++) {
          if (i & (1 << j)) { combinationSum += arr[j]; }
        }
        if (n === combinationSum) { return true; }
      }
      return false;
    };

    const Stars = (props) => {  
      let stars = [];  
      return (
        <div className="col-5">
            {_.range(props.numberOfStars).map(i =>
            <i key={i} className="fa fa-star"></i>
          )}
        </div>
      );
    };

    const Button = (props) => {
        let button;

      switch(props.answerIsCorrect) {
        case true:
          button = 
            <div className="col-2">
              <button className="btn btn-success"
                            onClick={props.acceptAnswer}>
                <i className="fa fa-check"></i>
              </button>
            </div>
            break;
        case false:
          button = 
            <div className="col-2">
              <button className="btn btn-danger">
                <i className="fa fa-times"></i>
              </button>
            </div>
            break;
        default:
          button = 
            <div className="col-2">
              <button className="btn"
                      disabled={props.selectedNumbers.length === 0}
                      onClick={props.checkAnswer}>
                    =
              </button>
            </div>
            break;
      }
      return (
        <div className="col-2 text-center">
            {button}
          <br/>
          <br/>
          <button onClick={props.redraw} className="btn btn-warning btn-sm"
                        disabled={props.redraws===0}>
            <i className="fa fa-refresh"></i> {props.redraws}
          </button>
        </div>
      );
    };

    const Answer = (props) => {
      return (
        <div className="col-5">
          {props.selectedNumbers.map((number, i) => 
                <span key={i}
                        onClick={() => props.unselectNumber(number)}>
                {number}
              </span>
          )}
        </div>
      );
    };

    const Numbers = (props) => {
        const numberClassName = (number) => {
        if(props.usedNumbers.indexOf(number) >= 0) {
            return 'used';
        }
        if(props.selectedNumbers.indexOf(number) >= 0) {
            return 'selected';
        }
      };

      updateNumber = (number) => {
        props.selectNumber(number);
      };

        return (
        <div className="card text-center">
          <div>
            {Numbers.list.map((number, i) => 
                <span key={i} className={numberClassName(number)} 
                    onClick={() => updateNumber(number)}>
                {number}
              </span>
            )}
          </div>
        </div>
      );
    };

    const DoneFrame = (props) => {
        return (
        <div className="text-center">
          <h2>{props.doneStatus}</h2>
        </div>
      );
    };

    Numbers.list = _.range(1,10);

    class Game extends React.Component {
        static randomNumber = () => 1 + Math.floor(Math.random() * 9);
        state = {
        selectedNumbers: [],
        randomNumberOfStars : Game.randomNumber(),
        answerIsCorrect: null,
        usedNumbers: [],
        redraws: 5,
        doneStatus: null,
      };

      selectNumber = (clickedNumber) => {
        if (this.state.selectedNumbers.indexOf(clickedNumber) >= 0) { return; }
        this.setState(prevState => ({
            answerIsCorrect: null,
            selectedNumbers: prevState.selectedNumbers.concat(clickedNumber)
        }));
      };

      unselectNumber = (clickedNumber) => {
        this.setState(prevState => ({
            answerIsCorrect: null,
            selectedNumbers: prevState.selectedNumbers
                                                            .filter(number => number !== clickedNumber)
        }));
      };

      checkAnswer = () => {
        this.setState(prevState => ({
            answerIsCorrect: prevState.randomNumberOfStars === 
            prevState.selectedNumbers.reduce((acc, n) => acc + n, 0)
        }));
      };

      acceptAnswer = () => {
        this.setState(prevState => ({
            usedNumbers: prevState.usedNumbers.concat(prevState.selectedNumbers),
          selectedNumbers: [],
          answerIsCorrect: null,
          randomNumberOfStars: Game.randomNumber(),
        }), this.updateDoneStatus);
      };

      redraw = () => {
        if (this.state.redraws === 0) { return; }
        this.setState(prevState => ({
            randomNumberOfStars: Game.randomNumber(),
          answerIsCorrect: null,
          selectedNumbers: [],
          redraws: prevState.redraws-1
        }), this.updateDoneStatus);
      };

      updateDoneStatus = () => {
        this.setState(prevState => {
            if(prevState.usedNumbers.length === 9) {
            return {doneStatus: 'Done, Nice!'};
          }
            if(prevState.redraws === 0 & !this.possibleSolutions(prevState)) {
            return {doneStatus: 'Game Over!'};
          }
        });
      };

      possibleSolutions = ({randomNumberOfStars, usedNumbers}) => {
        const possibleNumbers = _.range(1,10).filter(number => 
            usedNumbers.indexOf(number === -1)
        );

        return possibleCombinationSum(possibleNumbers, randomNumberOfStars);
      };

        render() {
        const {
            selectedNumbers, 
          randomNumberOfStars, 
          answerIsCorrect, 
          usedNumbers,
          redraws,
          doneStatus
        } = this.state;

        return (
            <div className="">
              <h3>Play Nine</h3>
            <hr />
            <div className="row">
              <Stars numberOfStars={randomNumberOfStars} />
              <Button selectedNumbers={selectedNumbers}
                            checkAnswer={this.checkAnswer}
                      answerIsCorrect={answerIsCorrect}
                      acceptAnswer={this.acceptAnswer}
                      redraw={this.redraw}
                      redraws={redraws} />
              <Answer selectedNumbers={selectedNumbers}
                            unselectNumber={this.unselectNumber} />
            </div>
            <br/>
            {doneStatus ?
                <DoneFrame doneStatus={doneStatus} /> :
              <Numbers selectedNumbers={selectedNumbers}
                        selectNumber={this.selectNumber}
                        usedNumbers={usedNumbers}/>
            }
            </div>
        );
      }
    }

    ReactDOM.render(<Game />, mountNode);
