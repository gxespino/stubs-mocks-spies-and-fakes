### Stubs, Mocks, Spies and Fakes
(Nothing is real anymore)

---

### TIL:

- Stubs
- Mocks
- Spies
- Fakes
- When, how and why to use either

---

### Background

+++?code=src/background.rb&lang=ruby

+++

```ruby
class Parser
  def self.parse(csv_file)
    ...
  end
end

class Loader
  def self.load!(parsed_data)
    ...
  end
end
```

---

### Stubs

```ruby
describe '#process' do
  it 'returns a successful Result object' do
    processor = Processor.new

    result = processor.process('test_file.csv')

    expect(result.successful?).to eq(true)
  end
end
```

No stubs in place. Object and it's tests are tightly _coupled_ to it's dependencies

+++

```ruby
describe '#process' do
  it 'returns a successful Result object' do
    processor  = Processor.new
    parsed     = double('parsed')
    loaded     = double('loaded')
    allow(Parser).to receive(:parse).and_return(parsed)
    allow(Loader).to receive(:load!).and_return(loaded)

    result = processor.process('test_file.csv')

    expect(result.successful?).to eq(true)
  end
end
```

@[4-5](Using RSpec's `double` method as a stand in Object)
@[6-7](_Stubbing_ our collaborators internal implementation with our test doubles)

---

Stubs are great for QUERY methods
But they don't suffice for COMMAND methods

+++

```ruby
def process(csv_file)
  begin
    parsed = parser.parse(csv_file)
    loader.load!(parsed)

    Result.new(:success)
  rescue ActiveRecord::RecordInvalid => invalid
    Result.new(:failed, invalid.record.errors)
  end
end
```

@[1-2, 5-10](Using stubs our test would still pass if we removed these two lines!)

+++

What should we do instead?

---

### Mocks

```ruby
describe '#process' do
  it 'returns a successful Result object' do
    processor  = Processor.new
    parsed     = double('parsed')
    loaded     = double('loaded')
    expect(Parser).to receive(:parse).and_return(parsed)
    expect(Loader).to receive(:load!).and_return(loaded)

    result = processor.process('test_file.csv')

    expect(result.successful?).to eq(true)
  end
end
```

@[4-5](Still using the same Doubles)
@[6-7](A mock is just like a stub, except that it doesn't just allow methods to be invoked; it expects it.)

+++

stubs = allow

mocks = expect

+++

But Mocks muddle up our tests by setting expectations out of order

+++

```ruby
describe '#process' do
  it 'returns a successful Result object' do
    processor  = Processor.new
    parsed     = double('parsed')
    loaded     = double('loaded')
    expect(Parser).to receive(:parse).and_return(parsed)
    expect(Loader).to receive(:load!).and_return(loaded)

    result = processor.process('test_file.csv')

    expect(result.successful?).to eq(true)
  end
end
```

@[3-5](Arrange)
@[9](Act)
@[11](Assert)
@[6-7](Arrange/Assert/Both?/WTH!?)

---

### Spies

```ruby
describe '#process' do
  it 'returns a successful Result object' do
    processor  = Processor.new
    parsed     = double('parsed')
    loaded     = double('loaded')
    allow(Parser).to receive(:parse).and_return(parsed)
    allow(Loader).to receive(:load!).and_return(loaded)

    result = processor.process('test_file.csv')

    expect(Parser).to have_received(:parse).with('test_file.csv')
    expect(Loader).to have_received(:load!).with(parsed)
    expect(result.successful?).to eq(true)
  end
end
```

@[3-7](Arrange)
@[9](Act)
@[11-13](Assert)
@[6-7, 11-12](Spy)

+++

Spies are ALSO stubs. They just also have an explicit expectation.

+++

Benefits of Spies:

- Tests are organized clearly in Arrange, Act, Assert <!-- .element: class="fragment" -->
- Easier to understand <!-- .element: class="fragment" -->
- Easier to extract repeated Arrange steps <!-- .element: class="fragment" -->

Downsides of Spies: <!-- .element: class="fragment" -->

- Some duplication (overcome this by extraction) <!-- .element: class="fragment" -->

+++

```ruby
describe '#process' do
  it 'returns a successful Result object' do
    processor  = Processor.new
    stub_parser_and_loader(parsed, loaded)

    result = processor.process('test_file.csv')

    expect(Parser).to have_received(:parse).with('test_file.csv')
    expect(Loader).to have_received(:load!).with(parsed)
    expect(result.successful?).to eq(true)
  end
end

def stub_parser_and_loader
  parsed = double('parsed')
  loaded = double('loaded')
  allow(Parser).to receive(:parse).and_return(parsed)
  allow(Loader).to receive(:load!).and_return(loaded)
end
```

---

### Fakes

---
