### Stubs, Mocks, Spies, Fakes and Doubles
(Nothing is real anymore)

---

### TIL:

- Stubs
- Mocks
- Spies
- Fakes
- Doubles

---

### Background

+++?code=src/background.rb&lang=ruby

@[0-17]
@[19-29]

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
@[1]
@[3-4](Using stubs our test would still pass if we removed these two lines!)

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

---

### Spies

---

### Fakes

---

### Doubles
