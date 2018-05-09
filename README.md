# React Simple Experiment

[![version][version]](http://npm.im/react-simple-experiment)
[![MIT License][mit license]](http://opensource.org/licenses/MIT)
[![Standard Version][standard version]](https://github.com/conventional-changelog/standard-version)
[![Size][size]](https://unpkg.com/react-simple-experiment)
[![Size gzip][size gzip]](https://unpkg.com/react-simple-experiment)

Simple A/B testing for React

## Usage

### Experiment & Variant

```js
import React from "react";
import { Experiment, Variant } from "react-simple-experiment";

export default function MyABTest() {
  return (
    <Experiment
      name="Experiment Name"
      onLoad={(name, variant) => console.log(name, variant)}
    >
      <Variant name="Variant 1 Name" weight={60}>
        <div>Variant 1</div>
      </Variant>

      <Variant name="Variant 2 Name" weight={20}>
        <div>Variant 2</div>
      </Variant>

      <Variant name="Variant 3 Name" weight={20}>
        <div>Variant 3</div>
      </Variant>
    </Experiment>
  );
}
```

Once a variant has been chosen the choice is persisted to browser storage (using [localForage]) to ensure the same variant is shown on return visits

### ExperimentQueryString

If you use react-router-dom in your project `<ExperimentQueryString />` component allows you can set experimant variants using a query string, like so:

Experiment query strings can now be formatted as below:  
`?exp=experimentId1||variantId&exp=experimentId2||variantId`

```js
import { ExperimentQueryString } from "react-simple-experiment";
import { Route } from "react-router-dom";

<Route component={ExperimentQueryString} />;
```

you can customise `querystringName` and `querystringDivider` using props:

```js
import { ExperimentQueryString } from "react-simple-experiment";
import { Route } from "react-router-dom";

<Route
  render={({ location }) => (
    <ExperimentQueryString
      querystringName="variant"
      querystringDivider="__"
      location={location}
    />
  )}
/>;
```

Experiment query strings can now be formatted as below:  
`?variant=experimentId1__variantId&variant=experimentId2__variantId`

### ExperimentManager

If you'd like to manipulate active experiment varaints within a component, perhaps for debugging, you can use the `<ExperimentManager/ >` component which takes a function for its children prop.  
The render function exposes the following four functions:

```js
getExperiments().then(experiments => {
  console.log(experiments);
  /*
    {
      "experimentId1": {
        "activeVariant": "control",
        "variants": {
          "control": 50,
          "variant1": 50
        }
      },
      "experimentId2": {
        "activeVariant": "variant1",
        "variants": {
          "control": 50,
          "variant1": 50
        }
      }
    }
  */
});

getExperiment(experimentId).then(variantId => {
  console.log(variantId);
  // variantId
});

setExperiment(experimentId, variantId).then(variantId => {
  console.log(variantId);
  // variantId
});

removeExperiment(experimentId).then(undefined => {});
```

and can be used within a component as a render prop like so:

```js
import { ExperimentManager } from "react-simple-experiment";

export default function MyExperimentManager() {
  return (
    <ExperimentManager
      render={({
        getExperiments,
        getExperiment,
        setExperiment,
        removeExperiment
      }) => {
        return /* custom logic.. */;
      }}
    />
  );
}
```

or you can pass it a component to enable using lifecycle methods:

```js
import { ExperimentManager } from "react-simple-experiment";

export default function MyExperimentManager() {
  state = {
    experiments: null
  };

  componentDidMount() {
    this.getExperiments();
  }

  getExperiments = (reload = false) => {
    this.props.getExperiments().then(experiments => {
      this.setState(() => ({ experiments })
      );
    });
  };

  render () {
    return (
      <div>
        {JSON.stringify(this.state.experiments, null, 2)}
      </div>
    );
  };
}

export default props => (
  <ExperimentManager
    location={props.location}
    component={MyExperimentManager}
    {...props} />
);
```

### License

MIT

[version]: https://img.shields.io/npm/v/react-simple-experiment.svg
[mit license]: https://img.shields.io/npm/l/react-simple-experiment.svg
[standard version]: https://img.shields.io/badge/release-standard%20version-brightgreen.svg
[size]: https://badges.herokuapp.com/size/npm/react-simple-experiment
[size gzip]: https://badges.herokuapp.com/size/npm/react-simple-experiment?gzip=true
[localforage]: https://github.com/localForage/localForage
