### Stubs, Mocks, Spies, Fakes and Doubles
#### (Nothing is real)

---

### TIL:

- Stubs
- Mocks
- Spies
- Fakes
- Doubles

---

### Background

```ruby
class Processor
  def initialize(parser: Parser, loader: Loader)
    @parser = parser
    @loader = loader
  end

  def process(csv_file)
    begin
      parsed = parser.parse(csv_file)
      loader.load!(parsed)

      Result.new(:success)
    rescue ActiveRecord::RecordInvalid => invalid
      Result.new(:failed, invalid.record.errors)
    end
  end
end

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

##### No stubs in place. Object and it's tests are tightly _coupled_ to it's dependencies

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

#### In this example we are dictating what Parser and Loader will return, decoupling Processor from the implementation details of it's collaborators. Parser and Loader can change, but Processor's tests won't and shouldn't break because they should only be concerned with how Processor works.

---

### Mocks

---

### Spies

---

### Fakes

---

### Doubles
