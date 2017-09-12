# react-find-sum-game
A simple game in react

    const Stars = (props) => {
      const numberOfStars = 1 + Math.floor(Math.random() * 9);

      let stars = [];  
      return (
        <div className="col-5">
          {_.range(numberOfStars).map(i =>
            <i key={i} className="fa fa-star"></i>
          )}
        </div>
      );
    };

    const Button = (props) => {
      return (
        <div className="col-2">
          <button>=</button>
        </div>
      );
    };

    const Answer = (props) => {
      return (
        <div className="col-5">...</div>
      );
    };

    const Numbers = (props) => {
      return (
        <div className="card text-center">
          <div>
            {Numbers.list.map((number, i) => 
              <span key={i}>{number}</span>
            )}
          </div>
        </div>
      );
    };

    Numbers.list = _.range(1,10);

    class App extends React.Component {
      render() {
        return (
          <div className="">
            <h3>Play Nine</h3>
            <hr />
            <div className="row">
              <Stars />
              <Button />
              <Answer />
            </div>
            <br/>
            <Numbers />
          </div>
        );
      }
    }

    ReactDOM.render(<App />, mountNode);
