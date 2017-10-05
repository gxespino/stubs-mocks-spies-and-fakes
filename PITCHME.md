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

@[4-5]
@[6-7]

---

### Mocks

---

### Spies

---

### Fakes

---

### Doubles
